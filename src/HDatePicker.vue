<template>
    <div ref="pickerContainer" class="relative">
        <label :for="id"
            class="relative block overflow-hidden rounded-xl border px-3 pt-3 shadow-sm focus-within:ring-4 transition-colors cursor-text"
            :class="[error ? 'border-red-200 focus-within:border-red-400 focus-within:ring-red-200' : 'border-gray-200 focus-within:border-gray-400 focus-within:ring-gray-200']">

            <div @click="togglePicker"
                class="peer h-9 w-full border-none bg-transparent p-0 placeholder-transparent focus:placeholder-gray-400 focus:border-transparent focus:outline-none focus:ring-0 sm:text-sm text-sm text-gray-800 flex items-center justify-between cursor-pointer">
                <span class="text-gray-700">{{ selectedDateFormatted.renderGematriya(true) }}</span>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-gray-400" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
                </svg>
            </div>

            <span
                class="absolute start-3 top-3 -translate-y-1/2 text-xs text-gray-400 transition-all peer-placeholder-shown:top-1/2 peer-placeholder-shown:text-sm peer-focus:top-3 peer-focus:text-xs">
                {{ label }}
            </span>
        </label>

        <div v-if="showPicker" class="border rounded-xl p-2 bg-white shadow-lg fixed z-[9999] w-[250px] mt-2">
            <!-- Header -->
            <div class="flex justify-between items-center mb-2 mt-0.5">
                <button @click="prevMonth"
                    class="px-2 py-1 bg-gray-100 hover:bg-gray-200 rounded transition-colors text-xs">
                    ▶
                </button>
                <div class="flex gap-2">
                    <span @click="toggleMonthSelection"
                        class="cursor-pointer text-sm font-semibold hover:text-gray-600 transition-colors">
                        {{ currentMonthName }}
                    </span>
                    <span @click="toggleYearSelection"
                        class="cursor-pointer text-sm font-semibold hover:text-gray-600 transition-colors">
                        {{ gematriya(currentYear) }}
                    </span>
                </div>
                <button @click="nextMonth"
                    class="px-2 py-1 bg-gray-100 hover:bg-gray-200 rounded transition-colors text-xs">
                    ◀
                </button>
            </div>

            <div v-if="showMonthSelection" class="grid grid-cols-4 gap-1 p-1">
                <button v-for="(month, index) in hebrewMonths" :key="index" @click="selectMonth(index + 1)"
                    class="py-1 px-2 rounded text-center text-xs transition-colors hover:bg-gray-200"
                    :class="{ 'bg-gray-900 text-white hover:bg-gray-700': index + 1 === selectedMonth }">
                    {{ month }}
                </button>
            </div>

            <div v-if="showYearSelection" class="grid grid-cols-4 gap-1 p-1">
                <button v-for="year in yearRange" :key="year" @click="selectYear(year)"
                    class="py-1 px-2 rounded text-center text-xs transition-colors hover:bg-gray-200"
                    :class="{ 'bg-gray-900 text-white hover:bg-gray-700': year === selectedYear }">
                    {{ gematriya(year) }}
                </button>
            </div>

            <div v-if="!showMonthSelection && !showYearSelection"
                class="grid grid-cols-7 text-center text-xs font-semibold mb-1">
                <span v-for="day in weekDays" :key="day" class="py-1 text-gray-600">{{ day }}</span>
            </div>
            <div v-if="!showMonthSelection && !showYearSelection" class="grid grid-cols-7 text-center">
                <span v-for="n in firstDayOffset" :key="'offset-' + n"></span>
                <button v-for="day in daysInMonth" :key="day"
                    class="py-1 mx-0.5 mb-0.5 rounded-full text-xs transition-colors hover:bg-gray-200"
                    :class="{ 'bg-gray-900 text-white hover:bg-gray-700': day === selectedDay }"
                    @click="selectDay(day)">
                    {{ hebrewDays[day - 1] }}
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue';
import { HDate, gematriya } from '@hebcal/core';
import dayjs from 'dayjs';

// Props and emit for v-model
const props = defineProps({
    modelValue: {
        type: String,
        required: true
    },
    label: {
        type: String,
        required: true
    },
    id: {
        type: String,
        required: true
    },
    error: {
        type: Boolean,
        default: false
    }
});
const emit = defineEmits(['update:modelValue']);

// State
const today = new HDate(dayjs(props.modelValue || new Date()).toDate());
const selectedDay = ref(today.getDate());
const selectedMonth = ref(today.getMonth());
const selectedYear = ref(today.getFullYear());
const showPicker = ref(false);
const showMonthSelection = ref(false);
const showYearSelection = ref(false);

// Ref for the picker container
const pickerContainer = ref(null);

// Close picker on outside click
const closePickerOnClickOutside = (event) => {
    if (pickerContainer.value && !pickerContainer.value.contains(event.target)) {
        showPicker.value = false;
        showMonthSelection.value = false;
        showYearSelection.value = false;
    }
};

onMounted(() => {
    document.addEventListener('click', closePickerOnClickOutside);
});

onUnmounted(() => {
    document.removeEventListener('click', closePickerOnClickOutside);
});

// Computed properties
const hebrewMonths = computed(() => {
    const months = ["ניסן", "אייר", "סיוון", "תמוז", "אב", "אלול", "תשרי", "חשוון", "כסלו", "טבת", "שבט", "אדר"];
    if (isLeapYear.value) {
        months.pop();
        months.splice(12, 0, "אדר א", "אדר ב");
    }
    return months;
});
const hebrewDays = ["א׳", "ב׳", "ג׳", "ד׳", "ה׳", "ו׳", "ז׳", "ח׳", "ט׳", "י׳", "י״א", "י״ב", "י״ג", "י״ד", "ט״ו", "ט״ז", "י״ז", "י״ח", "י״ט", "כ׳", "כ״א", "כ״ב", "כ״ג", "כ״ד", "כ״ה", "כ״ו", "כ״ז", "כ״ח", "כ״ט", "ל׳"];
const weekDays = ["א", "ב", "ג", "ד", "ה", "ו", "ש"];

const selectedDateFormatted = computed(() => {
    let month = selectedMonth.value;
    if (isLeapYear.value) {
        if (selectedMonth.value === 12) {
            month = 12;
        } else if (selectedMonth.value === 13) {
            month = 13;
        }
    }
    return new HDate(selectedDay.value, month, selectedYear.value);
});

const isLeapYear = computed(() => {
    return HDate.isLeapYear(selectedYear.value);
});

const currentYear = computed(() => selectedYear.value);

const currentMonthName = computed(() => {
    return hebrewMonths.value[selectedMonth.value - 1];
});

const daysInMonth = computed(() => {
    return new HDate(selectedYear.value, selectedMonth.value, 1).daysInMonth();
});

const firstDayOffset = computed(() => {
    const firstDay = new HDate(1, selectedMonth.value, selectedYear.value);
    return ((firstDay.getDay() + 1) + 6) % 7;
});

const yearRange = computed(() => {
    return Array.from({ length: 20 }, (_, i) => selectedYear.value - 10 + i);
});

// Watch for changes and emit Gregorian date
watch(selectedDateFormatted, (newDate) => {
    emit('update:modelValue', dayjs(newDate.greg()).format('YYYY-MM-DD'));
});

// Methods
const togglePicker = () => {
    showPicker.value = !showPicker.value;
    showMonthSelection.value = false;
    showYearSelection.value = false;
};

const toggleMonthSelection = () => {
    showMonthSelection.value = !showMonthSelection.value;
    showYearSelection.value = false;
};

const toggleYearSelection = () => {
    showYearSelection.value = !showYearSelection.value;
    showMonthSelection.value = false;
};

const selectDay = (day) => {
    selectedDay.value = day;
    showPicker.value = false;
};

const prevMonth = () => {
    if (selectedMonth.value === 1) {
        selectedMonth.value = 12;
        selectedYear.value--;
    } else {
        selectedMonth.value--;
    }
};

const nextMonth = () => {
    if (selectedMonth.value === 12) {
        selectedMonth.value = 1;
        selectedYear.value++;
    } else {
        selectedMonth.value++;
    }
};

const selectMonth = (month) => {
    selectedMonth.value = month;
    showMonthSelection.value = false;
};

const selectYear = (year) => {
    selectedYear.value = year;
    showYearSelection.value = false;
};
</script>

<style>
.transition-colors {
    transition: all 0.2s ease-in-out;
}
</style>

# Vue 3 Hebrew Date Picker

A Hebrew Date Picker component for Vue 3 that allows you to select Hebrew dates.

## Features

- Select Hebrew dates with intuitive month and day selection.
- Displays the selected date in the Hebrew calendar format.
- Allows switching between months and years.
- Fully customizable with props and events.

## Installation

You can install the component via npm:

```bash
npm i vue3-hebrew-date-picker
```

### Global Registration

In your `main.js`:
```javascript
import { createApp } from 'vue';
import App from './App.vue';
import HDatePicker from 'vue3-hebrew-date-picker';
const app = createApp(App);
app.component('HDatePicker', HDatePicker);
app.mount('#app');
```
### Local Registration

In your component:
```vue
<template>
    <HDatePicker v-model="selectedDate" label="Select Date" id="hebrew-datepicker" />
</template>

<script setup>
import { ref } from 'vue';
import HDatePicker from 'vue3-hebrew-date-picker';
const selectedDate = ref(''); 
</script>

``` 

## Props

| Prop        | Type    | Description                                           |
|-------------|---------|-------------------------------------------------------|
| `modelValue`| String  | The selected date in `YYYY-MM-DD` format              |
| `label`     | String  | The label text for the date picker                    |
| `id`        | String  | The ID attribute for the date picker input            |
| `error`     | Boolean | If set to `true`, shows an error state                |


## License

This project is licensed under the MIT License - see the LICENSE file for details.

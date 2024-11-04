<template>
    <div class="number-input-container">
        <label>{{ label }}:</label>
        <div class="input-range">
            <input type="number" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" :min="min" :max="max">
            <input type="range" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" :min="min" :max="max">
        </div>
    </div>
</template>
  
  
<script>
  export default {
    props: [ 'modelValue', 'label', 'min', 'max' ],
    emits: ['update:modelValue']
  };
</script>

<style scoped>
.number-input-container input[type="number"] {
    background-color: #F3F6FF;
    border: 1px solid #E0E0E0;
    border-radius: 5px;
    padding: 17px;
    width: calc(100% - 34px); 
    color: #15203D;
    outline: none;
    font-family: "SB Sans Text Medium";
    font-size: 15px; 
    line-height: 1.5; 
}

.number-input-container input[type="number"]:hover {
    background-color: #dae3ff;
}

.number-input-container input[type="range"] {
    -webkit-appearance: none;
    height: 6px;
    background: #D3D3D3;
    border-radius: 5px;
    outline: none;
}

.number-input-container input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 16px;
    height: 16px;
    background: #24ADED;
    border-radius: 50%;
    cursor: pointer;
}
.number-input-container {
    position: relative;
    margin-bottom: 20px;
}

.input-range {
position: relative;
}

.input-range input[type="range"] {
    position: absolute;
    bottom: -8px; 
    left: 0;
    width: 100%;
    -webkit-appearance: none;
    height: 6px;
    background: #D3D3D3;
    border-radius: 5px;
    outline: none;
}

.input-range input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 16px;
    height: 16px;
    background: #24ADED;
    border-radius: 50%;
    cursor: pointer;
}

.input-range input[type="range"]::-moz-range-thumb {
    width: 16px;
    height: 16px;
    background: #24ADED;
    border-radius: 50%;
    cursor: pointer;
}
</style>
  
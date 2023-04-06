<template>
    <button class="btn" @click="onClick">{{ label }}</button>
  </template>
  
  <script>
  export default {
    name: 'Button',
    props: {
      label: {
        type: String,
        required: true
      },
      onClick: {
        type: Function,
        required: true
      }
    }
  }
  </script>
  
  <style>
  .btn {
    background-color: #3498db;
    color: white;
    border: none;
    border-radius: 3px;
    padding: 5px 10px;
    cursor: pointer;
    position: left;
  }
  </style>
  
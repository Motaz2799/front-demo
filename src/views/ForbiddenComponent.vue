<template>
    <div class="forbidden-container">
      <h1 class="forbidden-title">403 Forbidden</h1>
      <p class="forbidden-message">Oops! You don't have permission to access this page.</p>
    </div>
  </template>
  
  <style>
  .forbidden-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    background-color: #FFA500;
  }
  
  .forbidden-title {
    font-size: 48px;
    font-weight: bold;
    color: #FFFFFF;
    margin-bottom: 20px;
  }
  
  .forbidden-message {
    font-size: 24px;
    color: #FFFFFF;
  }
  </style>
  
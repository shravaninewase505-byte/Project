@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap');

.layout-container {
  font-family: 'Inter', sans-serif; /* Enforces the requested Google Font */
  display: flex;
  gap: 20px;
  padding: 20px;
  background-color: #f8f9fa;
  height: 100vh;
}

.form-column {
  flex: 2;
  background: white;
  padding: 24px;
  border-radius: 8px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}

.chat-column {
  flex: 1;
  background: white;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}
/* Additional styling omitted for brevity */
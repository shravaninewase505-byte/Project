import { configureStore, createSlice } from '@reduxjs/toolkit';

const initialState = {
  hcp_name: '',
  interaction_type: 'Meeting',
  date: '',
  time: '',
  attendees: '',
  topics_discussed: '',
  sentiment: 'Neutral',
  outcomes: '',
  follow_up_actions: '',
};

const interactionSlice = createSlice({
  name: 'interaction',
  initialState,
  reducers: {
    updateField: (state, action) => {
      const { field, value } = action.payload;
      state[field] = value;
    },
    updateEntireForm: (state, action) => {
      return { ...state, ...action.payload };
    }
  }
});

export const { updateField, updateEntireForm } = interactionSlice.actions;

export const store = configureStore({
  reducer: {
    interaction: interactionSlice.reducer
  }
});
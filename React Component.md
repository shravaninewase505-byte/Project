import React, { useState } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { updateField, updateEntireForm } from './store';
import './LogInteractionScreen.css'; // Add Google Inter font here

const LogInteractionScreen = () => {
  const dispatch = useDispatch();
  const formData = useSelector((state) => state.interaction);
  const [chatInput, setChatInput] = useState('');
  const [chatHistory, setChatHistory] = useState([]);

  const handleInputChange = (e) => {
    dispatch(updateField({ field: e.target.name, value: e.target.value }));
  };

  const handleChatSubmit = async (e) => {
    e.preventDefault();
    if (!chatInput) return;

    // Add user message to UI
    setChatHistory([...chatHistory, { role: 'user', text: chatInput }]);

    // Call FastAPI / LangGraph backend
    const response = await fetch('/api/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        user_message: chatInput,
        current_form: formData
      })
    });

    const data = await response.json();
    
    // Update Redux state with AI-extracted data
    dispatch(updateEntireForm(data.updated_form));
    
    // Add AI reply to UI
    setChatHistory(prev => [...prev, { role: 'ai', text: data.ai_reply }]);
    setChatInput('');
  };

  const handleSaveToDB = async () => {
    await fetch('/api/interactions', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(formData)
    });
    alert('Interaction logged successfully!');
  };

  return (
    <div className="layout-container">
      {/* LEFT COLUMN: Structured Form */}
      <div className="form-column">
        <h2>Log HCP Interaction</h2>
        
        <div className="form-grid">
          <div className="form-group">
            <label>HCP Name</label>
            <input name="hcp_name" value={formData.hcp_name} onChange={handleInputChange} placeholder="Search or select HCP..." />
          </div>
          <div className="form-group">
            <label>Interaction Type</label>
            <select name="interaction_type" value={formData.interaction_type} onChange={handleInputChange}>
              <option value="Meeting">Meeting</option>
              <option value="Call">Call</option>
              <option value="Email">Email</option>
            </select>
          </div>
          {/* Add Date, Time, Attendees inputs similarly... */}
          <div className="form-group full-width">
            <label>Topics Discussed</label>
            <textarea name="topics_discussed" value={formData.topics_discussed} onChange={handleInputChange} rows="3" />
          </div>
        </div>
        
        <button className="save-btn" onClick={handleSaveToDB}>Save Interaction</button>
      </div>

      {/* RIGHT COLUMN: AI Assistant Chat */}
      <div className="chat-column">
        <div className="chat-header">
          <span className="icon">🤖</span> AI Assistant
        </div>
        <div className="chat-history">
          {chatHistory.map((msg, idx) => (
            <div key={idx} className={`chat-bubble ${msg.role}`}>
              {msg.text}
            </div>
          ))}
        </div>
        <form onSubmit={handleChatSubmit} className="chat-input-area">
          <input 
            type="text" 
            value={chatInput} 
            onChange={(e) => setChatInput(e.target.value)}
            placeholder="Log interaction details here..." 
          />
          <button type="submit">Log</button>
        </form>
      </div>
    </div>
  );
};

export default LogInteractionScreen;
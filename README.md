# AI-BY-RANIT# Requirements install karo
pip install openai gradio

# Run karo
python chatgpt_clone.py
# Ollama version (free)
import requests
import json

def chat_with_ollama(message, history):
    messages = [{"role": "user", "content": message}]
    
    response = requests.post('http://localhost:11434/api/generate',
                           json={
                               "model": "llama2",  # Ya koi bhi model
                               "prompt": message,
                               "stream": False
                           })
    
    return response.json()['response']
    # Ollama install karo
curl -fsSL https://ollama.ai/install.sh | sh

# Model download karo
ollama pull llama2
ollama pull mistral
# Voice Input/Output
# Image Generation
# File Upload
# Custom Themes
# Multiple Models
import streamlit as st
import requests
import json
from huggingface_hub import InferenceClient
import os

# Page Config
st.set_page_config(
    page_title="ChatGPT Clone",
    page_icon="🤖",
    layout="wide",
    initial_sidebar_state="expanded"
)

# HuggingFace FREE API (No OpenAI key needed!)
@st.cache_resource
def get_hf_client():
    return InferenceClient(model="microsoft/DialoGPT-medium")

class ChatGPTClone:
    def __init__(self):
        self.client = get_hf_client()
        if 'messages' not in st.session_state:
            st.session_state.messages = []
    
    def chat(self, user_input):
        # Add user message
        st.session_state.messages.append({"role": "user", "content": user_input})
        
        # Generate response
        try:
            response = self.client.text_generation(
                user_input,
                max_new_tokens=200,
                temperature=0.7,
                do_sample=True
            )
            
            ai_response = response.strip()
            st.session_state.messages.append({"role": "assistant", "content": ai_response})
            
        except Exception as e:
            st.session_state.messages.append({
                "role": "assistant", 
                "content": f"Error: {str(e)}"
            })

# Initialize Chatbot
chatbot = ChatGPTClone()

# Header
st.title("🤖 ChatGPT Clone")
st.markdown("**FREE • No API Key • GitHub Ready**")

# Sidebar
with st.sidebar:
    st.header("⚙️ Settings")
    temperature = st.slider("Creativity", 0.1, 1.0, 0.7)
    max_tokens = st.slider("Response Length", 50, 500, 200)

# Chat Interface
for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

# Chat Input
if prompt := st.chat_input("Type your message..."):
    with st.chat_message("user"):
        st.markdown(prompt)
    with st.chat_message("assistant"):
        with st.spinner("Thinking..."):
            chatbot.chat(prompt)
    st.rerun()

# Clear Chat
if st.sidebar.button("🗑️ Clear Chat", use_container_width=True):
    st.session_state.messages = []
    st.rerun()

# Footer
st.markdown("---")
st.markdown("*Powered by HuggingFace & Streamlit*")

# LLMs & Agents Project

This repository contains learning materials and a production-ready AI Writing application using AutoGen Studio and Streamlit.

## 📁 Project Structure

```
LLMs&Agents/
├── P02-S01-01-Basic_LLM.ipynb           # Introduction to LLM basics
├── P02-S01-02-Running_autogen.ipynb     # Running AutoGen framework
├── P02-S01-03-Two_standup_comedians.ipynb # Multi-agent conversation example
├── DeployWriters/                       # Production AI Writing application
│   ├── streamlit_selector_writer_app.py # Streamlit frontend application
│   ├── writers_team.json                # AutoGen team configuration
│   └── serve_fixed.py                   # Fixed AutoGen Studio API server
├── requirements.txt                     # Python dependencies
├── .gitignore                           # Git ignore file
├── README.md                            # Project documentation
└── venv/                                # Python virtual environment (not in git)
```

---

## 📚 Jupyter Notebooks

### P02-S01-01-Basic_LLM.ipynb
**Introduction to Large Language Models**

Covers fundamental concepts of LLMs including:
- Basic LLM architecture
- Prompt engineering basics
- API interactions with OpenAI
- Simple text generation examples

**Usage:**
```bash
jupyter notebook P02-S01-01-Basic_LLM.ipynb
```

### P02-S01-02-Running_autogen.ipynb
**Running AutoGen Framework**

Comprehensive guide to AutoGen framework featuring:
- AutoGen installation and setup
- Creating agents with different roles
- Agent communication patterns
- Task orchestration
- Multi-agent workflows

**Key Topics:**
- AssistantAgent configuration
- UserProxyAgent setup
- Team configurations
- Message passing between agents
- Termination conditions

**Usage:**
```bash
jupyter notebook P02-S01-02-Running_autogen.ipynb
```

### P02-S01-03-Two_standup_comedians.ipynb
**Multi-Agent Conversation Example**

Fun example demonstrating:
- Two-agent dialogue systems
- Role-playing with specialized prompts
- Creative writing with AI agents
- Conversation flow control

**Scenario:** Two AI agents roleplay as standup comedians, generating jokes and banter.

**Usage:**
```bash
jupyter notebook P02-S01-03-Two_standup_comedians.ipynb
```

---

## 🖋️ DeployWriters - AI Writing Application

A production-ready web application that uses multiple AI writers to generate content based on user requests.

### Architecture

```
User Request → Streamlit UI → Fixed API Server → AutoGen Team
                                                  ├─ Selector Agent
                                                  ├─ Technical Writer
                                                  └─ Creative Writer
```

### Components

#### 1. **streamlit_selector_writer_app.py**
**Frontend Web Application**

- **Framework:** Streamlit
- **Port:** Default Streamlit port (typically 8501)
- **Features:**
  - Clean, intuitive text input interface
  - Real-time response display with markdown rendering
  - Error handling and user feedback
  - Session state management
  - Debug mode for troubleshooting

**Configuration:**
```python
APP_TITLE = "🖋️ AI Writing Stylizer ✍️"
BASE_API_URL = "http://127.0.0.1:8084"
DEBUG_MODE = False  # Set to True for diagnostic output
```

**Key Functions:**
- `_normalize_messages()`: Handles various message format structures
- `_get_all_agent_messages()`: Extracts content from technical/creative writers
- Session state management for persistence

#### 2. **writers_team.json**
**AutoGen Team Configuration**

Defines a SelectorGroupChat team with:

**Agents:**
- **Technical Writer**: Specializes in clear, objective, structured content
  - Uses subtitles and technical terminology
  - Focuses on accuracy and clarity
  - Best for: explanations, documentation, technical topics

- **Creative Writer**: Specializes in engaging, imaginative content
  - Uses literary devices (metaphors, similes)
  - Focuses on style and tone
  - Best for: stories, creative writing, marketing

- **Selector Agent**: Automatically routes requests to appropriate writer
  - Analyzes user query intent
  - Chooses between technical and creative based on context

**Configuration Details:**
- **Model:** GPT-4o Mini (OpenAI)
- **Max Messages:** 10 per conversation
- **Termination:** Text mention ("TERMINATE") or max messages
- **Context:** Unbounded chat completion context

**System Prompts:**
```
Technical Writer: "Generate clear, concise, and objective text.
Structure with subtitles when appropriate. Use technical terms
accurately. After completing your writing, add TERMINATE on a new line."

Creative Writer: "Generate text that is engaging, imaginative,
and stylistically interesting. Use metaphors, similes, and literary
devices. After completing your writing, add TERMINATE on a new line."
```

#### 3. **serve_fixed.py**
**Fixed AutoGen Studio API Server**

Custom FastAPI server that fixes AutoGen Studio's message serialization issue.

**Problem Solved:**
Default AutoGen Studio doesn't include message `content` in JSON responses - only `source`, `models_usage`, and `metadata` fields are returned.

**Solution:**
- Custom serialization extracts content using `content` attribute or `to_text()` method
- Properly handles `RequestUsage` objects
- Returns complete messages with content included

**API Endpoint:**
```
GET /predict/{task}
```

**Response Format:**
```json
{
  "message": "Task successfully completed",
  "status": true,
  "data": {
    "task_result": {
      "messages": [
        {
          "source": "technical_writer",
          "content": "# Generated Content\n...",
          "models_usage": {
            "prompt_tokens": 73,
            "completion_tokens": 486
          },
          "metadata": {}
        }
      ],
      "stop_reason": "Text 'TERMINATE' mentioned"
    },
    "usage": "",
    "duration": 12.14
  }
}
```

**Key Functions:**
- `serialize_message()`: Extracts and formats message content
- `serialize_task_result()`: Formats complete task results
- `predict()`: Main API endpoint handler

---

## 🚀 Getting Started

### Prerequisites

```bash
python 3.8+
pip
virtualenv or venv
```

### Installation

1. **Clone the repository:**
```bash
git clone https://github.com/ReemOmer/LLMs-Agents.git
cd LLMs-Agents
```

2. **Create and activate virtual environment:**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies:**
```bash
pip install -r requirements.txt
```

4. **Configure your OpenAI API key:**
   - Edit `DeployWriters/writers_team.json`
   - Replace all instances of `"YOUR_OPENAI_API_KEY_HERE"` with your actual OpenAI API key

### Running the Application

**Terminal 1 - Start API Server:**
```bash
cd DeployWriters
export AUTOGENSTUDIO_TEAM_FILE="$(pwd)/writers_team.json"
source ../venv/bin/activate
uvicorn serve_fixed:app --host 127.0.0.1 --port 8084
```

**Terminal 2 - Start Streamlit App:**
```bash
cd DeployWriters
streamlit run streamlit_selector_writer_app.py
```

**3. Open your browser** to the Streamlit URL (typically http://localhost:8501)

---

## 💡 Usage Examples

### Technical Writing Request
```
Input: "Explain quantum computing"

Output: Structured article with:
- Introduction
- Key Concepts (Qubits, Entanglement, Quantum Gates)
- Quantum Algorithms
- Applications
- Current Challenges
- Conclusion
```

### Creative Writing Request
```
Input: "Write a story about a robot discovering emotions"

Output: Engaging narrative with:
- Character development
- Emotional arc
- Literary devices
- Descriptive language
- Creative plot structure
```

---

## 🔧 Configuration

### API Configuration

**File:** `streamlit_selector_writer_app.py`
```python
BASE_API_URL = "http://127.0.0.1:8084"  # API endpoint
DEBUG_MODE = False                       # Enable debug output
```

### Agent Configuration

**File:** `writers_team.json`

To modify agent behavior:
1. Edit `system_message` for each agent
2. Adjust `max_messages` for longer conversations
3. Change `model` to use different GPT models
4. Update `api_key` with your OpenAI key

### Model Selection

Current: `gpt-4o-mini`

To change:
```json
{
  "model": "gpt-4o",  // or "gpt-4-turbo", "gpt-3.5-turbo"
  "api_key": "your-api-key"
}
```

---

## 🐛 Troubleshooting

### Port Already in Use
```bash
lsof -ti:8084 | xargs kill -9
```

### No Content in Response
1. Ensure `serve_fixed.py` is running (not default AutoGen Studio)
2. Check `AUTOGENSTUDIO_TEAM_FILE` environment variable
3. Verify server is on port 8084
4. Enable DEBUG_MODE to see diagnostic info

### API Connection Errors
```bash
# Test API directly
curl "http://127.0.0.1:8084/predict/test"
```

### Agent Not Generating Content
Check system messages in `writers_team.json` - ensure they don't cause immediate termination.

### Streamlit App Not Loading
```bash
# Check if port is available
lsof -ti:8501

# Restart Streamlit
streamlit run streamlit_selector_writer_app.py --server.port 8502
```

---

## 📊 Project Status

✅ **Working Features:**
- API returns complete message content
- Streamlit displays formatted responses
- Technical Writer generating structured content
- Creative Writer generating engaging content
- Selector agent routing requests correctly
- TERMINATE handling working properly
- Error handling implemented
- Markdown rendering functional

🔧 **Configuration Options:**
- Debug mode available
- Customizable agent prompts
- Adjustable model selection
- Configurable termination conditions

---

## 🔐 Security Notes

**API Keys:**
- Current configuration includes OpenAI API key in `writers_team.json`
- **⚠️ Never commit API keys to version control**
- Use environment variables for production:

```bash
export OPENAI_API_KEY="your-key-here"
```

Update `writers_team.json`:
```json
{
  "api_key": "${OPENAI_API_KEY}"
}
```

---

## 📝 Development Notes

### Key Insights

1. **AutoGen Message Serialization Issue:**
   - Default AutoGen Studio doesn't serialize message content
   - Content accessed via methods (`to_text()`, `content` attribute)
   - Pydantic serialization strips content by default
   - Solution: Custom serialization in `serve_fixed.py`

2. **TERMINATE Handling:**
   - Agents must generate content BEFORE "TERMINATE"
   - System prompts critical for proper termination
   - Front-end strips "TERMINATE" from display

3. **Message Format Flexibility:**
   - Handles both list and dict message formats
   - Supports numeric key ordering ({"0": {}, "1": {}})
   - Robust content extraction from multiple locations

### Code Quality

- Type hints used throughout
- Comprehensive error handling
- Debug mode for diagnostics
- Session state for persistence
- Clean separation of concerns

---

## 🎯 Future Enhancements

### Potential Features
- [ ] Multiple writer selection (not just technical/creative)
- [ ] Conversation history
- [ ] Export to PDF/Word
- [ ] User authentication
- [ ] Cost tracking for API usage
- [ ] Response rating system
- [ ] Custom agent configuration UI
- [ ] Streaming responses
- [ ] Multi-language support
- [ ] Prompt templates library

### Performance Improvements
- [ ] Response caching
- [ ] Async API calls
- [ ] Load balancing for multiple users
- [ ] Rate limiting
- [ ] Response time optimization

---

## 📚 Resources

### AutoGen Documentation
- [AutoGen Official Docs](https://microsoft.github.io/autogen/)
- [AutoGen Studio Guide](https://microsoft.github.io/autogen/docs/autogen-studio/)

### Streamlit Documentation
- [Streamlit Docs](https://docs.streamlit.io/)
- [Streamlit API Reference](https://docs.streamlit.io/library/api-reference)

### OpenAI Documentation
- [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
- [GPT-4 Guide](https://platform.openai.com/docs/guides/gpt)

---

## 🤝 Contributing

This is a personal learning project, but suggestions and improvements are welcome!

### Development Setup
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

---

## 📄 License

This project is for educational purposes.

---

## 👤 Author

**Reem Elmahdi**
- Project: LLMs & Agents Learning
- Focus: Multi-agent systems, AutoGen framework, AI writing applications

---

## 🙏 Acknowledgments

- Microsoft AutoGen team for the framework
- OpenAI for GPT models
- Streamlit for the web framework
- Claude (Anthropic) for development assistance

---

## 📞 Support

For issues or questions:
1. Check the Troubleshooting section
2. Enable DEBUG_MODE for diagnostics
3. Test API with curl: `curl "http://127.0.0.1:8084/predict/test"`

---

**Last Updated:** November 12, 2025
**Version:** 1.0.0
**Status:** Production Ready ✅
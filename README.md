# Ollama + Open WebUI - GPU Docker Stack

Complete AI stack with Ollama (LLM backend) and Open WebUI (ChatGPT-like interface) with GPU support.

## Description

Self-hosted ChatGPT-like interface powered by Ollama, running locally with GPU acceleration.

## Prerequisites

- Docker
- Docker Compose
- Nvidia GPU with CUDA support
- Nvidia Container Toolkit

## Installation

```bash
# Start the stack
docker-compose up -d
```

## Architecture

```
+------------------+
|  Open WebUI  |  <- Web interface (localhost:3000)
|   :3000      |
\--------+---------+
       |
       v
+------------------+
|    Ollama    |  <- LLM backend + GPU
|   :11434     |
\------------------+
```

## Usage

### Access Web Interface

Navigate to: `http://localhost:3000`

1. Create account (first user becomes admin)
2. Select model from dropdown
3. Start chatting!

### Pull Models

```bash
# Via Web UI: Settings -> Models -> Pull Model

# Via CLI:
docker-compose exec ollama ollama pull llama2
docker-compose exec ollama ollama pull mistral
docker-compose exec ollama ollama pull codellama
```

## Configuration

### docker-compose.yml Example

```yaml
services:
  ollama:
    image: ollama/ollama:latest
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3000:8080"
    environment:
      - OLLAMA_API_BASE_URL=http://ollama:11434/api
    volumes:
      - open-webui-data:/app/backend/data
    depends_on:
      - ollama
```

### Environment Variables

Create `.env` file:
```env
# Open WebUI
WEBUI_SECRET_KEY=your_secret_key_here
OLLAMA_API_BASE_URL=http://ollama:11434/api

# Optional: Enable authentication
WEBUI_AUTH=true
```

## Features

### Open WebUI Features
- ChatGPT-like interface
- Multi-user support
- Conversation history
- Model switching
- Document upload
- RAG (Retrieval Augmented Generation)
- Markdown rendering
- Code syntax highlighting
- Dark/Light themes

### Ollama Features
- Multiple model support
- GPU acceleration
- Local inference
- Privacy-focused
- No internet required

## Model Management

### Via Web Interface

1. Settings -> Models
2. Pull new models
3. Delete unused models
4. Set default model

### Via CLI

```bash
# List models
docker-compose exec ollama ollama list

# Pull model
docker-compose exec ollama ollama pull llama2

# Remove model
docker-compose exec ollama ollama rm llama2
```

## Recommended Models

### General Purpose
- **llama2:7b** - Fast, good quality
- **mistral:7b** - Excellent performance
- **phi:2.7b** - Lightweight, fast

### Coding
- **codellama:7b** - Code generation
- **deepseek-coder:6.7b** - Code completion

### Large Models (Requires more VRAM)
- **mixtral:8x7b** - High quality
- **llama2:70b** - Best quality

## Common Commands

```bash
# Start stack
docker-compose up -d

# Stop stack
docker-compose down

# View logs
docker-compose logs -f

# Restart specific service
docker-compose restart ollama
docker-compose restart open-webui
```

## RAG (Document Chat)

### Upload Documents

1. Open chat
2. Click document icon
3. Upload PDF, TXT, DOCX, etc.
4. Ask questions about the document

### Supported Formats
- PDF
- TXT
- DOCX
- Markdown
- Code files

## User Management

### Create Admin User

First user to register becomes admin.

### Manage Users (Admin)

Settings -> Admin Panel -> Users:
- View all users
- Delete users
- Reset passwords
- Manage roles

## Performance

### GPU Requirements

Model VRAM requirements:
- **2B-7B models**: 6GB VRAM
- **13B models**: 12GB VRAM
- **70B models**: 48GB+ VRAM

### CPU Fallback

Remove GPU configuration for CPU-only:
```yaml
ollama:
  image: ollama/ollama:latest
  # Remove deploy section
```

## Troubleshooting

### Open WebUI Can't Connect to Ollama

```bash
# Check Ollama is running
docker-compose ps ollama

# Test Ollama API
curl http://localhost:11434/api/tags

# Check network connectivity
docker-compose exec open-webui ping ollama
```

### GPU Not Working

```bash
# Verify GPU access in container
docker-compose exec ollama nvidia-smi

# Check Nvidia Container Toolkit
nvidia-container-cli info
```

### Out of Memory

- Use smaller models (7B instead of 70B)
- Close other GPU applications
- Increase system swap

### Slow Response

- Check GPU utilization: `nvidia-smi`
- Try smaller model
- Reduce context length in chat

## Advanced Configuration

### Custom System Prompts

In Open WebUI:
1. Settings -> Models
2. Select model
3. Edit system prompt

### Context Window

Adjust in chat settings:
- Larger = better context understanding
- Smaller = faster response

### Temperature

Control randomness:
- Low (0.1-0.3) = Consistent, factual
- Medium (0.5-0.7) = Balanced
- High (0.8-1.0) = Creative

## Security Notes

**Important**:
- Runs completely local (no data sent to cloud)
- Set strong WEBUI_SECRET_KEY
- Use authentication in production
- Don't expose to internet without security
- Regular backups of conversations

## Use Cases

- Private AI assistant
- Code generation
- Document analysis
- Learning and experimentation
- Corporate AI (no data leakage)
- Offline AI development

## Resources

- [Open WebUI GitHub](https://github.com/open-webui/open-webui)
- [Ollama Documentation](https://github.com/ollama/ollama)
- [Model Library](https://ollama.ai/library)

## License

Personal project - Private use

---

**Note**: Ollama and Open WebUI are open-source projects. Models have their own licenses.

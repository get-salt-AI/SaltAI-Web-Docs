# Salt AI Documentation

Official documentation for Salt AI, available at [docs.salt.ai](https://docs.salt.ai).

## Quick Start

1. Clone the repository:
   ```bash
   git clone git@github.com:get-salt-AI/SaltAI-Web-Docs.git
   cd docs
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Preview locally:
   ```bash
   mkdocs serve
   ```

4. View at [http://localhost:8000](http://localhost:8000)

## Project Structure

```
docs/
├── nodes/          # Node documentation
├── images/         # Screenshots and assets
├── contributing/   # Contribution guides
└── ...            # Other documentation sections
```

## Contributing

1. Read our [Documentation Writing Guide](docs/contributing/writing-node-docs.md)
2. Create a new branch for your changes
3. Make your updates
4. Test locally with `mkdocs serve`
5. Submit a pull request

### Documentation Standards

- Follow the node documentation template
- Include screenshots for UI elements
- Write clear, concise instructions
- Test all examples

## Built With

- [MkDocs](https://www.mkdocs.org/) - Documentation framework
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) - Theme and extensions
- GitHub Pages - Hosting

## Local Development

### Requirements

- Python 3.7+
- pip

### Commands

```bash
# Install dependencies
pip install -r requirements.txt

# Start local server
mkdocs serve

# Build static site
mkdocs build
```

## Need Help?

- Review our [Writing Guide](docs/contributing/writing-node-docs.md)
- Open an issue for questions
- Join our community discussions

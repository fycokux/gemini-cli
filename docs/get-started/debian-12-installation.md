# Install Gemini CLI on Debian 12 (Bookworm)

This guide walks you through installing Gemini CLI on Debian 12 (Bookworm). By
the end, you will have a working Gemini CLI installation with authentication
configured.

## Prerequisites

Before installing Gemini CLI, ensure your Debian 12 system is up to date:

```bash
sudo apt update && sudo apt upgrade -y
```

## Step 1: Install Node.js 20

Gemini CLI requires Node.js version 20 or higher. Debian 12 ships with an older
version, so you need to install Node.js from the NodeSource repository.

### Option A: Install Node.js from NodeSource (recommended)

1.  Install the required dependencies:

    ```bash
    sudo apt install -y ca-certificates curl gnupg
    ```

2.  Add the NodeSource repository:

    ```bash
    sudo mkdir -p /etc/apt/keyrings
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
    ```

3.  Install Node.js:

    ```bash
    sudo apt update
    sudo apt install -y nodejs
    ```

4.  Verify the installation:

    ```bash
    node --version
    # Should output v20.x.x or higher

    npm --version
    # Should output a version number
    ```

### Option B: Install Node.js using nvm

[nvm](https://github.com/nvm-sh/nvm) (Node Version Manager) allows you to manage
multiple Node.js versions.

1.  Install nvm:

    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
    ```

2.  Reload your shell:

    ```bash
    source ~/.bashrc
    ```

3.  Install Node.js 20:

    ```bash
    nvm install 20
    nvm use 20
    ```

4.  Verify the installation:

    ```bash
    node --version
    npm --version
    ```

## Step 2: Install Gemini CLI

Install Gemini CLI globally using npm:

```bash
npm install -g @google/gemini-cli
```

Verify the installation:

```bash
gemini --version
```

## Step 3: Authenticate

Run Gemini CLI to start the authentication process:

```bash
gemini
```

When prompted, choose your preferred authentication method:

1.  **Login with Google** (recommended): Uses your Google account. This method
    includes a free tier of 60 requests/min and 1,000 requests/day.

2.  **Gemini API Key**: Use an API key from
    [Google AI Studio](https://aistudio.google.com/apikey).

    ```bash
    export GEMINI_API_KEY="YOUR_API_KEY"
    gemini
    ```

3.  **Vertex AI**: For enterprise use with Google Cloud.

    ```bash
    export GOOGLE_API_KEY="YOUR_API_KEY"
    export GOOGLE_GENAI_USE_VERTEXAI=true
    gemini
    ```

For more details, see the [Authentication Guide](./authentication.md).

## Step 4: Configure (optional)

Create a configuration directory:

```bash
mkdir -p ~/.gemini
```

Create a settings file for persistent configuration:

```bash
cat > ~/.gemini/settings.json << 'EOF'
{
  "theme": "Default"
}
EOF
```

For more configuration options, see the
[Configuration Guide](./configuration.md).

## Optional: Set up sandboxing with Docker

Sandboxing provides additional security by isolating Gemini CLI operations.

1.  Install Docker:

    ```bash
    sudo apt install -y docker.io
    sudo systemctl enable docker
    sudo systemctl start docker
    ```

2.  Add your user to the docker group (log out and back in after):

    ```bash
    sudo usermod -aG docker $USER
    ```

3.  Enable sandboxing:

    ```bash
    export GEMINI_SANDBOX=docker
    gemini
    ```

For more information, see the [Sandbox Guide](../cli/sandbox.md).

## Troubleshooting

### Permission errors when installing npm packages globally

If you encounter permission errors with `npm install -g`, you can configure npm
to use a local directory:

```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

Then reinstall Gemini CLI:

```bash
npm install -g @google/gemini-cli
```

### Node.js version is too old

If `node --version` shows a version below 20, ensure you followed the Node.js
installation steps correctly. You may need to remove the system Node.js first:

```bash
sudo apt remove nodejs npm
```

Then follow the NodeSource or nvm installation instructions again.

### Authentication browser doesn't open

If the browser doesn't open automatically during Google authentication, copy the
URL displayed in the terminal and paste it into your browser manually.

For headless systems without a graphical browser, use an API key instead:

```bash
export GEMINI_API_KEY="YOUR_API_KEY"
gemini
```

### SSL certificate errors

Ensure your system has up-to-date CA certificates:

```bash
sudo apt install -y ca-certificates
sudo update-ca-certificates
```

## Next steps

- Read the [Quickstart Guide](./index.md) to learn basic usage.
- Explore [Examples](./examples.md) for common use cases.
- Configure [MCP Servers](../tools/mcp-server.md) for custom integrations.

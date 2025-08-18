# Justin’s macOS Terminal Cheat Sheet

_Last updated: 2025-08-15_

Quick reference for the commands you run a lot (plus some trickier ones). Copy/paste freely. 👇

---

## Table of Contents
- [Navigation & Files](#navigation--files)
- [Conda Environments](#conda-environments)
- [Python & Jupyter](#python--jupyter)
- [TensorBoard](#tensorboard)
- [Git](#git)
- [Node / npm / pnpm / Vite / Next.js / Tailwind](#node--npm--pnpm--vite--nextjs--tailwind)
- [nvm (Node Version Manager)](#nvm-node-version-manager)
- [Astral **uv**](#astral-uv)
- [Ollama](#ollama)
- [Docker & Containers](#docker--containers)
- [Open-WebUI](#open-webui)
- [Java & Spark (local setup)](#java--spark-local-setup)
- [SSH & SCP](#ssh--scp)
- [cURL](#curl)
- [Whisper (CLI)](#whisper-cli)
- [FastAPI / Uvicorn](#fastapi--uvicorn)
- [Chainlit](#chainlit)
- [Zip / Archive](#zip--archive)
- [Misc Handy Zsh Things](#misc-handy-zsh-things)
- [Danger Zone (use with care)](#danger-zone-use-with-care)

---

## Navigation & Files
```bash
pwd                 # print current directory
ls                  # list files
ls -a               # include hidden files
cd <dir>            # change directory
mkdir <name>        # make directory
```

---

## Conda Environments
```bash
conda env list                       # show envs
conda create --name <env> python=3.12
conda activate <env>
conda deactivate
```

Common:
```bash
conda activate cs330
conda activate experimental
conda activate tts
conda activate deep_research
```

---

## Python & Jupyter
```bash
python --version
python file.py
python -m pip install <pkg>          # pip via current python
jupyter notebook                     # launch notebooks
```

---

## TensorBoard
```bash
tensorboard --logdir logs/
```

---

## Git
```bash
git init
git add .
git commit -m "Message"
git push -u origin main

git fetch origin
git pull origin main
git config --global credential.helper 'cache --timeout=3600'
git push --force-with-lease origin main   # safer force push
```

Remove committed node_modules (then push):
```bash
git rm -r --cached node_modules
echo "node_modules" >> .gitignore
git add .gitignore
git commit -m "chore: ignore node_modules"
git push
```

---

## Node / npm / pnpm / Vite / Next.js / Tailwind
Project lifecycle:
```bash
# Vite React app
npm create vite@latest my-react-app -- --template react
cd my-react-app
npm install
npm run dev

# Next.js (App Router) with pnpm + TS + Tailwind
pnpm create next-app@latest ross-roads-adventure   --ts --eslint --tailwind --app --src-dir --use-pnpm   --import-alias "@/*"

cd ross-roads-adventure
pnpm install
pnpm dev
```

Tailwind init (various ways):
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
# If tailwind binary missing:
npx --ignore-existing tailwindcss init -p
```

Fix broken npm deps quickly:
```bash
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

With pnpm (start fresh):
```bash
rm -rf node_modules pnpm-lock.yaml
pnpm install
pnpm dev
```

---

## nvm (Node Version Manager)
```bash
# install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
. "$HOME/.nvm/nvm.sh"

# install/use Node 22
nvm install 22
nvm use 22
node -v
npm -v
```

---

## Astral **uv**
```bash
# install uv (script)
curl -LsSf https://astral.sh/uv/install.sh | sh

uv --version
uv sync                         # sync virtual env and deps
uv run script.py                # run with managed env
uvx --refresh --from "langgraph-cli[inmem]" --with-editable . --python 3.12 langgraph dev
```

---

## Ollama
Basics:
```bash
ollama list
ollama run <model>              # e.g., ollama run gemma3
ollama pull <model>
ollama rm <model>:<tag>
ollama show --modelfile <model> # dump a Modelfile
```

Create a variant with custom params (example):
```bash
cat > Modelfile <<'EOF'
FROM gpt-oss:latest
PARAMETER think low             # set default reasoning if supported
EOF

ollama create gpt-oss-low -f Modelfile
ollama run gpt-oss-low
```

HTTP API quick test:
```bash
curl http://localhost:11434/api/generate   -d '{ "model": "gpt-oss:20b", "prompt": "What is water made of?" }'
```

---

## Docker & Containers
Run Docling Serve (with UI):
```bash
docker run -p 5001:5001 -e DOCLING_SERVE_ENABLE_UI=true   quay.io/docling-project/docling-serve
```

Open-WebUI (persistent volume, restart always):
```bash
docker run -d -p 3000:8080   -v open-webui:/app/backend/data   --name open-webui   --restart always   ghcr.io/open-webui/open-webui:main

docker exec -it open-webui bash    # shell into container
docker rm -f open-webui            # remove container
docker pull ghcr.io/open-webui/open-webui:main
```

---

## Open-WebUI
```bash
# python package approach
pip install --upgrade open-webui
open-webui serve
```

Docker approach — see above.

---

## Java & Spark (local setup)
Install JDKs:
```bash
brew install openjdk@11
brew install openjdk@8
```

Environment (zsh):
```bash
# in ~/.zshrc
export JAVA_HOME="/opt/homebrew/opt/openjdk@11"
export SPARK_HOME="$HOME/bin/spark/spark-3.1.1-bin-hadoop3.2"
export PATH="$JAVA_HOME/bin:$PATH"
source ~/.zshrc
java -version
```

Download & unpack Spark:
```bash
curl -O http://archive.apache.org/dist/spark/spark-3.1.1/spark-3.1.1-bin-hadoop3.2.tgz
tar -xzf spark-3.1.1-bin-hadoop3.2.tgz
pip install pyspark findspark
```

---

## SSH & SCP
SSH on custom port:
```bash
ssh -p 5018 user@hostname
```

SCP upload/download with custom port:
```bash
# upload local -> remote
scp -r -P 5018 /local/path user@host:/remote/path

# download remote -> local
scp -r -P 5018 user@host:/remote/path /local/path
```

---

## cURL
```bash
curl http://127.0.0.1:5001/ui/
curl -X POST "http://localhost:8000/add_task"   -H "Content-Type: application/json"   -d '{"title":"Task","notes":"From curl","due":"2025-07-27T17:00:00Z"}'
```

---

## Whisper (CLI)
```bash
# prepare (homebrew ffmpeg once)
brew install ffmpeg

# run
whisper "01 - Welcome.mp3" --model turbo
```

---

## FastAPI / Uvicorn
```bash
pip install fastapi uvicorn
uvicorn app:app --reload --port 8000
```

---

## Chainlit
```bash
pip install -r requirements.txt
chainlit run app.py
```

---

## Zip / Archive
```bash
zip -r submission.zip submission
zip -r src.zip src
```

---

## Misc Handy Zsh Things
Search command history:
```bash
history | grep <term>            # e.g., history | grep tensorboard
```

Generate a bcrypt hash (for web auth):
```bash
htpasswd -bnBC 10 "" "YourPassword" | tr -d ':
'
```

Show Python / pip paths:
```bash
which python
which pip
```

Aliases / venv activation (example):
```bash
alias pip='"/Applications/ComfyUI.app/Contents/Resources/uv/macos/uv" pip'
source "/Users/justin/Documents/ComfyUI/.venv/bin/activate"
```

---

## Danger Zone (use with care)
```bash
git push --force origin main           # overwrites remote history
rm -rf node_modules package-lock.json  # deletes local deps
docker rm -f <container>               # force remove container
```

---

### Notes
- Prefer `--force-with-lease` over `--force` when you must overwrite remote history.
- Keep `JAVA_HOME` and `SPARK_HOME` in your `~/.zshrc` so they persist across shells.
- For pnpm/Next.js+Tailwind stacks, keep Node version consistent (use `nvm`).


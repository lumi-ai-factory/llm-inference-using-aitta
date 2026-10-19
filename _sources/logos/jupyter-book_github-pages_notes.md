# Deploying a Jupyter Book on GitHub Pages
These steps describe how to create and publish a Jupyter Book as a static site using GitHub Pages.
---
## 1. Prepare the Book Configuration
1. Create the Jupyter Book configuration files in your repo:
- `_toc.yml` → defines the **table of contents**  
- `_config.yml` → defines **site settings and options**
---
## 2. Set Up a Python Environment
1. Create a virtual environment:
```bash
python3 -m venv .venv
```
2. Activate the environment and install required packages:
```bash
# Activate (Linux/macOS)
source .venv/bin/activate
# Activate (Windows PowerShell)
.venv\Scripts\Activate.ps1
# Install packages
pip install "jupyter-book<2" "ghp-import==2.1.0"
```
---
## 3. Build the Jupyter Book
Run the build command:
```bash
jupyter-book build PATH_SOURCE
```
Replace PATH_SOURCE with the path to your book root (e.g., `.` if you're in the repo root).  
This generates the HTML site in `_build/html`

---
## 4. Publish the Book to GitHub Pages

### Manual Deployment
Deploy the built HTML to GitHub Pages:
```bash
ghp-import -n -p -f _build/html
```
* `-n` → disables Jekyll
* `-p` → pushes to GitHub
* `-f` → force overwrites existing gh-pages branch

**Note:** Steps 3 & 4 need to be done every time you make changes to the content, unless you set up automated deployment (see below).

### Automated Deployment with GitHub Actions
To automatically build and deploy on every push:

1. Create `.github/workflows/deploy.yml` in your repository:
```yaml
name: Deploy Jupyter Book to GitHub Pages

on:
  push:
    branches:
      - main  # change to 'master' if that's your default branch

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.x'
    
    - name: Install dependencies
      run: |
        pip install "jupyter-book<2" "ghp-import==2.1.0"
        # Add any other dependencies your notebooks need here
    
    - name: Build the Jupyter Book
      run: |
        jupyter-book build .
    
    - name: Deploy to GitHub Pages
      run: |
        ghp-import -n -p -f _build/html
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

2. Configure GitHub repository permissions:
   - Go to **Settings** → **Actions** → **General**
   - Under **Workflow permissions**, select **"Read and write permissions"**

3. Verify GitHub Pages settings:
   - Go to **Settings** → **Pages**
   - Ensure **Source** is set to **"Deploy from a branch"**
   - Ensure **Branch** is set to **`gh-pages`** / **`/ (root)`**

4. Commit and push the workflow file:
```bash
git add .github/workflows/deploy.yml
git commit -m "Add GitHub Actions workflow for automated deployment"
git push
```

Now your site will automatically rebuild and deploy whenever you push changes to your main branch!

You can monitor deployment progress in the **Actions** tab of your GitHub repository.


# Docker + AWS Workshop Quick Guide: Local Hugo Setup

## Introduction

This guide is designed for the Docker team to set up a local Hugo environment for the AWS Modernization Workshop. This will allow team members to preview content changes locally before submitting Pull Requests to the main repository.

## Workshop Details

* **Workshop Studio URL**: [AWS Modernization with Docker Workshop](https://catalog.us-east-1.prod.workshops.aws/workshops/0be61c95-28ed-4670-a1fd-fcc32e6b4b11)
* **AWS Modernization Workshop URL**: [AWS Modernization with Docker Workshop](https://docker.awsworkshop.io/)
* **Repository URL**: [GitHub - aws-samples/aws-modernization-with-docker](https://github.com/aws-samples/aws-modernization-with-docker)

## Prerequisites

* Git installed on your local machine
* Hugo extended version installed (see installation steps below)
* GitHub CLI (gh) installed (optional, but recommended)
* Basic familiarity with command line operations
* GitHub account with access to the repository

## Step 1: Install GitHub CLI (gh) - Optional but Recommended

GitHub CLI makes it easier to fork repositories and manage pull requests from the command line.

### For macOS:

```
brew install gh
```

### For Windows:

```
winget install --id GitHub.cli
# OR
choco install gh
```

### For Linux:

```
# Debian/Ubuntu
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh

```

After installation, authenticate with your GitHub account:

```
gh auth login
```

Follow the prompts to complete the authentication process.

## Step 2: Install Hugo Extended Version

### For macOS:

```
brew install hugo
```

### For Windows:

```
choco install hugo-extended
```

### For Linux:

```
wget https://github.com/gohugoio/hugo/releases/download/v0.92.0/hugo_extended_0.92.0_Linux-64bit.deb
sudo dpkg -i hugo_extended_0.92.0_Linux-64bit.deb
```

Verify installation:

```
hugo version
```

Make sure it shows "extended" in the version information.

## Step 3: Fork and Clone the Repository

### Using GitHub CLI:

```
# Login with github
gh auth login

# Fork the repository
gh repo fork aws-samples/aws-modernization-with-docker --clone=true

# This will automatically clone your fork to your local machine
cd aws-modernization-with-docker
```

### Using GitHub Web Interface and Git:

1. Go to https://github.com/aws-samples/aws-modernization-with-docker
2. Click the "Fork" button in the top-right corner
3. Clone your fork to your local machine:

```
git clone https://github.com/YOUR-USERNAME/aws-modernization-with-docker.git
cd aws-modernization-with-docker
```

1. Add the original repository as an upstream remote to keep your fork in sync:

```
git remote add upstream https://github.com/aws-samples/aws-modernization-with-docker.git
```

## Step 4: Initialize and Update Submodules

The workshop uses themes as Git submodules. Initialize and update them:

```
git submodule init
git submodule update
```

## Step 5: Run Hugo Locally

Start the Hugo development server:

```
hugo server -D
```

This will start a local server, typically at http://localhost:1313/

## Step 6: Understanding the Folder Structure

The AWS Workshop structure typically follows this pattern:

```
workshop-repository/
├── content/                  # Main content directory
│   ├── _index.md            # Home page content
│   ├── 010_introduction/    # First section (note the numeric prefix for ordering)
│   │   ├── _index.md        # Section landing page
│   │   ├── 11_subsection.md # Subsection content
│   │   └── images/          # Images specific to this section
│   ├── 020_section_name/    # Second section
│   │   ├── _index.md
│   │   ├── 21_page1.md
│   │   ├── 22_page2.md
│   │   └── images/
│   └── 030_section_name/    # Third section
├── static/                   # Static assets (global images, CSS, JS)
│   └── images/              # Global images used across sections
└── config.toml              # Hugo configuration file
```

### Key Points About the Structure:

1. **Numeric Prefixes**: Folders and files use numeric prefixes (e.g., `010_`, `020_`) to control the order they appear in the navigation.
2. **_index.md Files**: Every section folder must contain an `_index.md` file that serves as the landing page for that section.
3. **Images Organization**:     - Section-specific images go in an `images/` folder within that section    - Global images used across multiple sections go in the `static/images/` directory

## Step 7: Front Matter and Content Organization

### Front Matter Explained

Front matter is YAML metadata at the top of each Markdown file that controls how Hugo processes the content. Here's an example:

```
---
title: "Introduction to Docker"
chapter: false
weight: 10
---
```

### Essential Front Matter Parameters:

1. **title**: The page title displayed in the browser and navigation    `yaml    title: "Your Page Title"`
2. **weight**: Controls the order of pages within a section (lower numbers appear first)    `yaml    weight: 10`
3. **chapter**: Determines if the page is a chapter start (typically used in `_index.md` files)    `yaml    chapter: true  # For section landing pages    chapter: false # For regular content pages`

### Example of Section _index.md:

```
---
title: "Introduction"
chapter: true
weight: 10
---

# Introduction

Welcome to the introduction section of the Docker workshop...
```

### Example of Content Page:

```
---
title: "Installing Docker"
chapter: false
weight: 11
---

## Installing Docker

Follow these steps to install Docker on your system...
```

## Step 8: Adding a New Section

To add a new section to the workshop:

1. Create a new directory with a numeric prefix in the `content/` directory:
```
mkdir content/040_new_section
```
2. Create an `_index.md` file in the new directory:
```
touch content/040_new_section/_index.md
```
3. Add the required front matter to the `_index.md` file:
```markdown

* * *
title: "New Section Title"    chapter: true    weight: 40
* * *
# New Section Title

Introduction text for your new section...
```

1. Create an images directory for section-specific images:
```
mkdir content/040_new_section/images
```
2. Add content pages with appropriate weights:
```
touch content/040_new_section/41_first_page.md
touch content/040_new_section/42_second_page.md
```

## Step 9: Referencing Images

### For Global Images:

1. Place the image in the `static/images/` directory:
```
static/images/architecture-diagram.png
```
2. Reference the image using an absolute path from the root:  

```
![Architecture Diagram](/images/architecture-diagram.png)
```

## Step 10: Making Content Changes

1. Create a new branch for your changes:
```
git checkout -b feature/your-feature-name
```
2. Navigate to the content directory:
```
cd content
```
3. Edit the Markdown files in the appropriate sections following the structure and front matter guidelines above.
4. . As you make changes, Hugo will automatically refresh the local server, allowing you to see your changes in real-time.

## Step 11: Preview Your Changes

1. Open your web browser and navigate to http://localhost:1313/
2. Navigate through the workshop to verify your changes appear correctly
3. Check both desktop and mobile views to ensure responsive design works properly

## Step 12: Prepare for Pull Request

1. Sync your fork with the upstream repository to avoid conflicts:
```
git fetch upstream
git merge upstream/main
```

2. Commit your changes:
```
git add .
git commit -m "Description of changes made"
```
3. Push your branch to GitHub:
```
git push origin feature/your-feature-name
```
4. Create a Pull Request:

###  Using GitHub CLI:   
```
gh pr create --title "Your PR title" --body "Description of your changes"
```


### Using GitHub Web Interface:    

 Go to https://github.com/YOUR-USERNAME/aws-modernization-with-docker    
- Click "Pull Requests" > "New Pull Request"    
- Select your branch and provide a detailed description of your changes
  Wait for review and address any feedback from maintainers

## Common Issues and Troubleshooting

### Hugo Version Mismatch

If you encounter errors related to Hugo version, check the required version in the repository (usually in README.md or .hugo-version file) and install that specific version.

### Submodule Issues

If the theme doesn't load correctly:

```
git submodule update --init --recursive
```

### Content Not Updating

If changes aren't reflecting in the browser: 
1. Clear your browser cache 
2. Restart the Hugo server with:
```
hugo server --disableFastRender
```

### Front Matter Issues

If your page doesn't appear in the navigation: - Check that the front matter is properly formatted with `---` delimiters - Verify the `weight` parameter is set correctly - Ensure there are no YAML syntax errors

### Image Not Displaying

If images don't appear: - Check the path is correct (relative for section images, absolute for global) - Verify the image file exists in the specified location - Check for case sensitivity in the filename

### New Section Not Appearing

If a new section doesn't appear: - Ensure the directory has a numeric prefix - Verify an `_index.md` file exists in the directory - Check that the `weight` parameter is set in the front matter

## Best Practices for Workshop Content

1. **Consistent Naming**: Use consistent naming conventions for files and directories
2. **Logical Weights**: Assign weights logically to maintain proper ordering
3. **Image Organization**: Keep section-specific images in their respective folders
4. **Front Matter**: Include all required front matter parameters in every file
5. **Preview Changes**: Always preview changes locally before submitting a PR
6. **Consistency**: Maintain consistent formatting throughout the workshop
7. **Screenshots**: Use clear, up-to-date screenshots when necessary
8. **Code Blocks**: Use proper syntax highlighting for code blocks
9. **Instructions**: Provide clear step-by-step instructions
10. **Validation**: Include validation steps so users can confirm they've completed tasks correctly

## Contact Information

For questions or issues related to this workshop, please contact the workshop maintainers through GitHub issues on the repository.

# Bash Prompt Customization Guide

## Understanding Your Current Prompt Format

Your desired prompt format: `pi@homelab:~/GitRepo/lab/kubernetes-fundamentals$`

This breaks down to:
- `pi` - Username
- `@` - Separator
- `homelab` - Hostname
- `:` - Separator
- `~/GitRepo/lab/kubernetes-fundamentals` - Current directory path
- `$` - Prompt symbol ($ for regular user, # for root)

## Quick Solution

### Option 1: Add to ~/.bashrc (Permanent)

Open your `.bashrc` file:
```bash
nano ~/.bashrc
```

Add this line at the end of the file:
```bash
PS1='\u@\h:\w\$ '
```

Save and exit (Ctrl+X, then Y, then Enter), then reload:
```bash
source ~/.bashrc
```

### Option 2: Test Immediately (Temporary)

Just run this command to test:
```bash
export PS1='\u@\h:\w\$ '
```

This will change your prompt immediately but won't persist after logout.

## PS1 Variable Explained

The `PS1` variable controls your bash prompt appearance. Here are the special characters:

| Code | Meaning | Example |
|------|---------|---------|
| `\u` | Username | pi |
| `\h` | Hostname (short) | homelab |
| `\H` | Hostname (full) | homelab.local |
| `\w` | Full working directory | ~/GitRepo/lab/kubernetes-fundamentals |
| `\W` | Basename of working directory | kubernetes-fundamentals |
| `\$` | $ for user, # for root | $ |
| `\d` | Date (e.g., "Tue May 26") | Tue May 26 |
| `\t` | Time (24-hour HH:MM:SS) | 14:30:45 |
| `\T` | Time (12-hour HH:MM:SS) | 02:30:45 |
| `\@` | Time (12-hour am/pm) | 02:30 PM |
| `\n` | Newline | - |
| `\\` | Backslash | \ |

## Enhanced Prompt Examples

### Example 1: Add Colors (Recommended)

```bash
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
```

Result: Green username@hostname, blue directory
- Green text: `\[\033[01;32m\]...\[\033[00m\]`
- Blue text: `\[\033[01;34m\]...\[\033[00m\]`
- Reset color: `\[\033[00m\]`

### Example 2: Add Time

```bash
PS1='[\t] \u@\h:\w\$ '
```

Result: `[14:30:45] pi@homelab:~/GitRepo/lab/kubernetes-fundamentals$`

### Example 3: Add Git Branch (Very Useful!)

First, add this function to your `.bashrc`:
```bash
# Function to show git branch
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}
```

Then set your PS1:
```bash
PS1='\u@\h:\w\[\033[01;33m\]$(parse_git_branch)\[\033[00m\]\$ '
```

Result: `pi@homelab:~/GitRepo/lab/kubernetes-fundamentals(main)$`

### Example 4: Multi-line Prompt

```bash
PS1='\u@\h:\w\n\$ '
```

Result:
```
pi@homelab:~/GitRepo/lab/kubernetes-fundamentals
$ 
```

### Example 5: Fancy Prompt with Colors and Git

```bash
# Add this function first
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}

# Then set PS1
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[01;33m\]$(parse_git_branch)\[\033[00m\]\$ '
```

Result: Colorful prompt with git branch showing when in a git repository

## Color Reference

Common color codes you can use:

| Color | Code |
|-------|------|
| Black | `\[\033[0;30m\]` |
| Red | `\[\033[0;31m\]` |
| Green | `\[\033[0;32m\]` |
| Yellow | `\[\033[0;33m\]` |
| Blue | `\[\033[0;34m\]` |
| Purple | `\[\033[0;35m\]` |
| Cyan | `\[\033[0;36m\]` |
| White | `\[\033[0;37m\]` |
| Bold Black | `\[\033[01;30m\]` |
| Bold Red | `\[\033[01;31m\]` |
| Bold Green | `\[\033[01;32m\]` |
| Bold Yellow | `\[\033[01;33m\]` |
| Bold Blue | `\[\033[01;34m\]` |
| Bold Purple | `\[\033[01;35m\]` |
| Bold Cyan | `\[\033[01;36m\]` |
| Bold White | `\[\033[01;37m\]` |
| Reset | `\[\033[00m\]` |

## Step-by-Step Instructions

### For Ubuntu Server on Raspberry Pi

1. **Open your bashrc file:**
   ```bash
   nano ~/.bashrc
   ```

2. **Find existing PS1 configuration (if any)**
   - Look for lines starting with `PS1=`
   - Comment them out by adding `#` at the start of the line

3. **Add your custom prompt at the end:**
   ```bash
   # Custom prompt
   PS1='\u@\h:\w\$ '
   ```

4. **Save and exit:**
   - Press `Ctrl+X`
   - Press `Y` to confirm
   - Press `Enter`

5. **Reload the configuration:**
   ```bash
   source ~/.bashrc
   ```

### Verify It Works

After reloading, your prompt should now look like:
```
pi@homelab:~/GitRepo/lab/kubernetes-fundamentals$
```

Try navigating to different directories:
```bash
cd /tmp
# Should show: pi@homelab:/tmp$

cd ~
# Should show: pi@homelab:~$

cd ~/GitRepo/lab/kubernetes-fundamentals
# Should show: pi@homelab:~/GitRepo/lab/kubernetes-fundamentals$
```

## Recommended Configuration for Your HomeLab

Here's what I recommend for your Kubernetes learning environment:

```bash
# Add to ~/.bashrc

# Function to show git branch
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
}

# Function to show kubectl context (very useful for K8s!)
kube_context() {
    if [ -f ~/.kube/config ]; then
        local context=$(kubectl config current-context 2>/dev/null)
        if [ -n "$context" ]; then
            echo "[k8s:$context]"
        fi
    fi
}

# Custom prompt with colors, git branch, and k8s context
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[01;33m\]$(parse_git_branch)\[\033[01;36m\]$(kube_context)\[\033[00m\]\$ '
```

This will show:
- Username and hostname in green
- Current directory in blue
- Git branch in yellow (when in a git repo)
- Kubernetes context in cyan (when kubectl is configured)

Example: `pi@homelab:~/GitRepo/lab/kubernetes-fundamentals(main)[k8s:kubernetes-admin@kubernetes]$`

## Troubleshooting

### Prompt Not Changing

If your prompt doesn't change after editing `.bashrc`:

1. **Make sure you reloaded:**
   ```bash
   source ~/.bashrc
   ```

2. **Check if you're using bash:**
   ```bash
   echo $SHELL
   ```
   Should show `/bin/bash`

3. **Check if .bashrc is being loaded:**
   Add this to test:
   ```bash
   echo "bashrc loaded"
   ```
   Then open a new terminal. You should see "bashrc loaded"

### Prompt Looks Weird with Colors

If colors show strange characters like `[01;32m`:

- Your terminal may not support ANSI colors
- Try the simple prompt without colors:
  ```bash
  PS1='\u@\h:\w\$ '
  ```

### Git Branch Not Showing

Make sure:
1. You're in a git repository
2. Git is installed: `git --version`
3. The function is defined before PS1 in your .bashrc

## Testing Before Committing

Always test your prompt before adding to `.bashrc`:

```bash
# Test this first
export PS1='\u@\h:\w\$ '

# If you like it, add to ~/.bashrc
echo "PS1='\u@\h:\w\$ '" >> ~/.bashrc
```

## Backup Your Current Config

Before making changes, backup your current `.bashrc`:

```bash
cp ~/.bashrc ~/.bashrc.backup
```

If something goes wrong, restore it:
```bash
cp ~/.bashrc.backup ~/.bashrc
source ~/.bashrc
```

## Additional Customization

### Shorten Long Paths

If your paths get too long, you can limit the directory depth:

```bash
# Show only last 2 directories
PROMPT_DIRTRIM=2
PS1='\u@\h:\w\$ '
```

Example: Instead of `~/GitRepo/lab/kubernetes-fundamentals`, shows `lab/kubernetes-fundamentals`

### Different Prompts for Root vs User

Add this to automatically show # for root:

```bash
if [ "$EUID" -eq 0 ]; then
    PS1='\[\033[01;31m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]# '
else
    PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
fi
```

This shows root in red, regular user in green.

## Complete Example for HomeLab

Here's a complete, ready-to-use configuration for your `.bashrc`:

```bash
# ==========================================
# Custom Bash Prompt Configuration
# ==========================================

# Colors
COLOR_RESET='\[\033[00m\]'
COLOR_GREEN='\[\033[01;32m\]'
COLOR_BLUE='\[\033[01;34m\]'
COLOR_YELLOW='\[\033[01;33m\]'
COLOR_CYAN='\[\033[01;36m\]'
COLOR_RED='\[\033[01;31m\]'

# Git branch function
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}

# Kubernetes context function
kube_context() {
    if command -v kubectl &> /dev/null && [ -f ~/.kube/config ]; then
        local context=$(kubectl config current-context 2>/dev/null)
        if [ -n "$context" ]; then
            echo " [k8s:$context]"
        fi
    fi
}

# Limit directory depth display
PROMPT_DIRTRIM=3

# Set prompt based on user
if [ "$EUID" -eq 0 ]; then
    # Root prompt (red)
    PS1="${COLOR_RED}\u@\h${COLOR_RESET}:${COLOR_BLUE}\w${COLOR_YELLOW}\$(parse_git_branch)${COLOR_CYAN}\$(kube_context)${COLOR_RESET}# "
else
    # Regular user prompt (green)
    PS1="${COLOR_GREEN}\u@\h${COLOR_RESET}:${COLOR_BLUE}\w${COLOR_YELLOW}\$(parse_git_branch)${COLOR_CYAN}\$(kube_context)${COLOR_RESET}\$ "
fi

# ==========================================
# End Custom Prompt Configuration
# ==========================================
```

## Quick Reference Card

Save this for quick access:

```bash
# Basic prompt
PS1='\u@\h:\w\$ '

# With colors
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '

# Test before saving
export PS1='your-prompt-here'

# Add to bashrc permanently
nano ~/.bashrc
# (add PS1 line at end)
source ~/.bashrc

# Backup before changes
cp ~/.bashrc ~/.bashrc.backup

# Restore if needed
cp ~/.bashrc.backup ~/.bashrc
```

---

**Pro Tip for CKA Exam**: Keep your prompt simple during the exam! The basic format `\u@\h:\w\$ ` is clean and won't distract you. Save the fancy prompts for your development environment.

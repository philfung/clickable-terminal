# 🖱️ Clickable Terminal
Navigate your directories like a pro. This setup lets you click icon-enhanced folders and filenames to move through your filesystem.

![Video Project 1](https://github.com/user-attachments/assets/319e85dd-617d-40d2-9b86-7f4ba7931096)
---


## 1. Prereqs
Ensure you have the necessary tools installed:
* Install **[Kitty Terminal](https://sw.kovidgoyal.net/kitty/binary/)**.
* Install **eza**, a modern `ls` replacement. 
    ```
    brew install eza
    ```

---

## 2. Configure Kitty Open Actions
Add the following to `~/.config/kitty/open-actions.conf`. This defines what happens when you click a directory link (it sends a `cd` command to your terminal).

```conf
# Handle directory clicks
protocol file
mime inode/directory
action send_text normal,application \x15cd "${FILE_PATH}" && ls\r
```

## 3. Update your Shell Config
Add this logic to your `~/.zshrc`. It wraps eza to generate the clickable links and adds a "Go Up" button to every ls output.

```bash # Kitty terminal clickable navigation logic
if [[ "$TERM" == "xterm-kitty" ]]; then

    function kitty_ls_with_back() {
        # 1. Prepare the parent directory path
        local parent_dir="$(dirname "$PWD")/"
    
        # 2. Run eza with hyperlink support
        # We pass "$@" so flags like -la still work
        eza --icons --hyperlink "$@"

        # 3. Print the clickable "Go Up" link in Bold Cyan
        # \e[1;36m = Bold Cyan | \e[0m = Reset
        printf '\e]8;;file://%s\e\\\e[1;36m[.. ↑Go Up]\e[0m\e]8;;\e\\\n' "$parent_dir"
    }

    # Override the default ls alias
    alias ls="kitty_ls_with_back"
fi
```

## 4. Finalize
Refresh your shell to apply the changes:
```
source ~/.zshrc
```

# Aliases

```bash
# General aliases
alias k='kubecolor --kubecolor-theme=protanopia-dark' # kubectl
alias tc='printf "\033[H\033[2J"' # clear terminal without losing history

# Directory navigation
alias cdl='cdl() { cd "$1" && ls; }; cdl' # change directory and list files
alias cdir='cdir() { mkdir "$1" && cd "$1"; }; cdir' # create directory and change into it
alias ...='cd ../..'
alias ....='cd ../../..'

# Kubie aliases
alias kubsts='kubie info depth' # get the current kubie shell depth
alias kubsta="kubie ctx \$(kubectl config get-contexts 2>/dev/null | awk '/\*/ {print \$2}')" # spawn kubie shell

# Grep aliases
alias gi=' grep -i'
alias gri='grep -ri'

# Git aliases
alias gpnb='git push --set-upstream origin $(git rev-parse --abbrev-ref HEAD)' # git push new branch
alias gpfwl='git push --force-with-lease' # git push force with lease
alias gam='git add . && git commit -m' # git add and commit
alias gdp="git checkout $(git remote show origin | grep -i "head" | awk '{ print $3 }') && git pull" # git pull from default branch
```

# Snippets

## Quick notes
```bash

# create quick notes. If no argument is given, it creates a note with the current date as filename, else it creates a note with the given argument as filename
notes() {
  if [[ "$1" == "-h" || "$1" == "--help" ]]; then
    echo "notes - a small collection of functions used to create and navigate quick notes"
    echo
    echo "Usage:"
    echo "notes -h | --help - shows this help message"
    echo "notes - creates a note with the current date as filename"
    echo "notes [filename] - creates a note with the given filename"
    echo 
    echo "lnotes - returns a list of all created notes as stdout"
    echo "onotes - opens a note using fzf"
    echo "snotes - opens a note using fzf and allows for word search"
    return 0
  elif [[ -z $1 ]]; then
    nvim "$HOME/notes/$(date +"%Y-%m-%d").md"
  else
    if [[ -f "$HOME/notes/$1.md" ]]; then
      echo "File already exists. Please choose a different name."
      return 1
    else
      nvim "$HOME/notes/$1.md"
    fi
  fi
}

alias lnotes='ls $HOME/notes' # list notes

# fzf that shows all files in the notes dir
onotes() {
  local file
  file=$(ls /Users/patrickmendes/notes | fzf --preview 'bat --style=numbers --color=always /Users/patrickmendes/notes/{} || cat /Users/patrickmendes/notes/{}')
  [ -n "$file" ] && nvim "/Users/patrickmendes/notes/$file"
}

# fzf that shows all files in the notes dir and allows for word search - extended version of `onotes`
snotes() {
  local result
  result=$(rg --color=always --line-number --no-heading . /Users/patrickmendes/notes 2> /dev/null | \
    fzf --ansi --preview 'bat --style=numbers --color=always --highlight-line {2} {1}' \
        --preview-window=right:50% --delimiter : --nth 3..)

  if [[ -n "$result" ]]; then
    local file
    local lineno
    file=$(echo "$result" | cut -d: -f1)
    lineno=$(echo "$result" | cut -d: -f2)
    nvim +"$lineno" "$file"
  fi
}

```

## Teleport App opener
```bash
# alias that takes arg as the search term when opening teleport
tapp() {
  search=$(IFS='+'; echo "$*")
  open -a "Firefox" "https://teleport.example.ch/web/cluster/teleport.example.ch/resources?search=$search&sort=name%3Aasc"
}
```



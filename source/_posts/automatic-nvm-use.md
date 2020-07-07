---
title: Automatic NVM Use
date: 2020-07-07 12:35:46
categories:
  - Article
tags:
  - NVM
  - Node
cover: 'https://i.imgur.com/WI8xkga.jpg'
---
# Background
As a Javascript Engineer I do alot of works with various node versions throughout the week, one day I could be using the old version like `v9.7.1`, and another day I could be debugging some problems on newer applications that uses node `v14.4.0` or something. You get the idea.

Therefore using a version manager tools such as [NVM](https://github.com/nvm-sh/nvm) is just a natural thing to do and I've been using it AFAIK for more than a year. Throughout that time I keep tweaking the whole experience because I find some pretty ineffective things that could be improved. Like for example the way it's loaded is blocking which means it slows my new instance of terminal even when it's not node related. And some other minor teeny tiny tweaks.

One of the tweak is to automatically run `nvm use` whenever there's a `.nvmrc` file in the directory, and that's what this article is gonna be about because from all the tweaks that I did on my local NVM, this is the most helpful one, at least in my opinion.

# Codes
## .zshrc
```sh
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

## .bashrc
```sh
find-up () {
    path=$(pwd)
    while [[ "$path" != "" && ! -e "$path/$1" ]]; do
        path=${path%/*}
    done
    echo "$path"
}

cdnvm(){
    cd "$@";
    nvm_path=$(find-up .nvmrc | tr -d '\n')

    if [[ ! $nvm_path = *[^[:space:]]* ]]; then

        declare default_version;
        default_version=$(nvm version default);

        if [[ $default_version == "N/A" ]]; then
            nvm alias default node;
            default_version=$(nvm version default);
        fi

        if [[ $(nvm current) != "$default_version" ]]; then
            nvm use default;
        fi

        elif [[ -s $nvm_path/.nvmrc && -r $nvm_path/.nvmrc ]]; then
        declare nvm_version
        nvm_version=$(<"$nvm_path"/.nvmrc)

        declare locally_resolved_nvm_version
        locally_resolved_nvm_version=$(nvm ls --no-colors "$nvm_version" | tail -1 | tr -d '\->*' | tr -d '[:space:]')

        if [[ "$locally_resolved_nvm_version" == "N/A" ]]; then
            nvm install "$nvm_version";
        elif [[ $(nvm current) != "$locally_resolved_nvm_version" ]]; then
            nvm use "$nvm_version";
        fi
    fi
}
alias cd='cdnvm'
```
# Usage
You can just simply copy the code above and put it on your respective `.zshrc` or `.bashrc` file (depends on what you're using) after the NVM initialization . The code above will automatically detect whether a `.nvmrc` file is exist in your current directory and if it is it'll run `nvm use`, and if you navigate to other directory where it has no `.nvmrc` it'll revert the node version to your default.

# Closing
I find this extremely helpful because I tend to forgot what my current node version is and it might messed up the current project by introducing incompatible syntax and packages. The code above is not written by me, I find it [here](https://github.com/nvm-sh/nvm#deeper-shell-integration). So thanks to its awesome documentation. Cheers!

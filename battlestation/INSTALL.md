# Installation

## Fresh installation setup

Working with my BattleStation, I require a certain set of apps on every installation. Here is my guide for installing my BattleStation from scratch.



### Additional Software

#### TL;DR;

I will publish a nice bash script here to run this all in one go...
Someday maybe ;)

#### Using APT

```bash
sudo apt update && sudo apt install vim git build-essential sl fortune cowsay lolcat lutris 
```
The most obvious list of apps I need, most of all is Vim and Git.
Some fun, the kids love `sl` and the coloring of `lolcat`. For simple gaming I use Lutris, it makes it very easy to access my GoG library.
I install the Kubernetes CLI and Helm via Homebrew. Which takes us to the next phase:

#### Using Homebrew

For my Bash prompt I really like Starship, which is very easily installed with Homebrew.
I also install mdless to read markdown documents in the terminal. For working with my homelab I need the Kubernetes CLI and for easy deployments Helm.

To install Homebrew, go to [the Homebrew website](https://brew.sh) or their [Github repository](https://github.com/Homebrew/brew) and follow the instructions. Or, like me, do this:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

echo >> ~/.bashrc
echo eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)" >> ~/.bashrc
```

Setup my favorite tools:
```bash
brew install mdless kubernetes-cli helm starship
```



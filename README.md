# jameskr.dev

a repo for my personal website!

## dev environment - make sure hugo is installed

1. Run `git submodule update --init --recursive` to clone the theme 
2. Run `brew install hugo` to install hugo (or whatever package manager you use)
3. Run `hugo server` to start the server

## environment variables
| variable | default value | usage |
| -------- | ------------- | ----- |
| `HUGO_PARAMS_UMAMI_ENABLED` | `false` | Enable/disable [umami analytics](https://umami.is) |
| `HUGO_PARAMS_UMAMI_WEBSITEID` | `''` | Website ID from umami dashboard |
| `HUGO_PARAMS_UMAMI_SCRIPTURL` | `''` | umami instance script.js URL |
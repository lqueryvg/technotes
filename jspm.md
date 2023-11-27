# yarn / npm

    # npm                 # yarn

    npm install           yarn
    npm install fred      yarn add fred
    npm install -D fred   yarn add -D fred
    npm install -g fred   yarn global add fred   # global

Most other commands are the same.

## yarn

    yarn upgrade {package}
    yarn init -y
    yarn workspaces run clean

- yarn hoists everything to top level node_modules
- packages are visible (usable) anywhere within tree
- only creates local `node_modules` if local `package.json` specifies a different version

## import from other workspaces

- import is done via the workspace name, not the folder name

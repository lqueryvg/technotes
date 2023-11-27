# Prettier recommendations

https://prettier.io/docs/en/integrating-with-linters.html#docsNav

- prettier for formatting, eslint for code quality
- USE eslint-config-prettier
  - turns off any lint rules which are handled by prettier _recommended_
- DON'T USE eslint-plugin-prettier
  - runs prettier as an eslint rule (it results in lots of red in code as you type)
  - run prettier --check instead

prettier-eslint - runs prettier then eslint --fix

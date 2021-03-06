# Octopus Energy Dashboard
[![Netlify Status](https://api.netlify.com/api/v1/badges/adebf5ed-d67f-4113-8fd8-338d58ac996b/deploy-status)](https://app.netlify.com/sites/octopus-energy/deploys)

The dashboard on Octopus Energy's website gives half-hour usage. In order to learn my own patterns of usage, I was more interested in a bigger picture, capturing an entire month at a time. Thus was born this dashboard.

API Keys and meters data is saved in localStorage and all quries and calculations are sent from the [client interface](https://octopus-energy.netlify.app/).

## Sample Dashboard

![Dashboard example](./public/example.png)

## Todo

- [X] Set-up project
- [X] Build ~~robust~~ calendar with fetched data
- [X] Save user data to localStorage (never sent to anyone)
- [X] Add Gas metrics
- [X] Add Elect metrics
- [ ] Give some trends info
- [ ] Quick month navigation
- [X] Learn about my energy usage!
- [X] Favicon
- [ ] Styling / hiding inputs

## Disclaimer

This project is provided as is. **Do not** share your API keys with anyone.

---

## VUE.js Commands
```
yarn install # Installs dependencies
yarn serve # Compiles and hot-reloads for development
yarn build # Compiles and minifies for production
```
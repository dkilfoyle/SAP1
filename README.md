# Quasar App

## Dev Setup

1. Debugging

   a. Add to build section of quasar.conf.js

   `{ devtools: 'source-map' }`

   b. Add to launch.json

   `"sourceMaps": true, "sourceMapPathOverrides": { "webpack:///*": "${webRoot}/*"`

# vue-example-project

### Dockerizing your Vue app
#### Dockerfile

```dockerfile
# build stage
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# production stage
FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
COPY prod_nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### prod_nginx.conf
```conf
user                    nginx;
worker_processes        1;
error_log               /var/log/nginx/error.log warn;
pid                     /var/run/nginx.pid;
events {
    worker_connections  1024;
}

http {
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    log_format          main '$remote_addr - $remote_user [$time_local] "$request" '
                             '$status $body_bytes_sent "$http_referer"'
                             '"$http_user_agent" "$http_x_forwarded_for"';
    access_log          /var/log/nginx/access.log main;
    sendfile            on;
    keepalive_timeout   65;
    server {
        listen          80;
        server_name     _ default_server;
        index           index.html;
        location / {
            root        /usr/share/nginx/html;
            index       index.html;
            try_files   $uri $uri/ /index.html;
        }
    }
}
```


```js
/*  plugins/vuetify.js */
import Vue from 'vue';
import Vuetify from 'vuetify';
import 'vuetify/dist/vuetify.min.css';

Vue.use(Vuetify);

export default new Vuetify({
    theme:{
        dark: true
    }
});
```

```js
/* main.js */
import tmdb from 'tmdb-agent'
import VueAxios from 'vue-axios'

Vue.prototype.$tmdb = tmdb({
    apiKey: '****************'
})

Vue.use(VueAxios, http)
```

```vue
<template>
  <v-app>
    <Navbar/>
    <v-main>
      <router-view/>
    </v-main>
  </v-app>
</template>

<script>
import Navbar from './components/Navbar';

export default {
  name: 'App',

  components: {
    Navbar,
  },

  data: () => ({
    
  }),
};
</script>
```


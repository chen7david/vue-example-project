# vue-example-project

### Adding Vuetify to your Project
Run the command:
```cmd
vue add vuetify
```

#### example App.vue

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

#### Example Navbar Component
```vue
<template>
    <nav>
        <v-navigation-drawer
            app
            dark
            v-model="drawer"
        >       
            <v-row justify="center" class="my-5">
                <v-avatar size="150px">
                    <img src="https://www.iconfinder.com/data/icons/business-avatar-1/512/8_avatar-512.png" alt="">
                </v-avatar>
                <v-list-item three-line class="text-center">
                    <v-list-item-content>
                        <v-list-item-title>David M.Chen</v-list-item-title>
                        <v-list-item-subtitle>Teacher</v-list-item-subtitle>
                        <v-list-item-subtitle>
                            <v-chip class="font-weight-bold" color="success">$1005</v-chip>
                        </v-list-item-subtitle>
                    </v-list-item-content>
                </v-list-item>
                <v-switch
                v-model="dark"
                color="purple"
                hide-details
                inset
            ></v-switch>
            </v-row>
            
            <v-list>
                <v-list-item
                    v-for="link of links"
                    :key="link.to"
                    router
                    :to="link.to"
                >
                    <v-list-item-action>
                        <v-icon>{{link.icon}}</v-icon>
                    </v-list-item-action>
                    <v-list-item-content>{{link.name}}</v-list-item-content>
                </v-list-item>
            </v-list>
        </v-navigation-drawer>
        <v-app-bar
            app
            dark
        >
            <v-app-bar-nav-icon @click="drawer = !drawer"/>
            <v-spacer></v-spacer>
            <v-btn tile router to="/login">Login</v-btn>
            <v-btn tile router to="/register">Register</v-btn>
        </v-app-bar>
    </nav>
</template>

<script>
export default {
    name: 'Navbar',
    data: () => ({
        isLoading: false,
        drawer: true,
        dark: true,
        links: [
            {
                name: 'Movies',
                icon: 'mdi-movie',
                to: '/movies'
            },
            {
                name: 'Shows',
                icon: 'mdi-monitor',
                to: '/shows'
            },
            {
                name: 'Search',
                icon: 'mdi-magnify',
                to: '/search'
            },
            {
                name: 'Documentation',
                icon: 'mdi-file',
                to: '/documentation'
            },
        ]
    }),
    methods: {
    },
    watch: {
      dark(dark){
        this.$vuetify.theme.dark = dark
      }
    }
}
</script>
```

## Dockerizing your Vue app

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

#### .dockerignore
```.dockerignore
node_modules
npm-debug.log
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


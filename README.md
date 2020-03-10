# Vuexi
Work in progress, so breaking changes in API are possible. Please consider locking dependency if this library fit your needs!

Vuexi is a toolset of small but very useful functions to reduce vuex code base for async flows and adds optional caching, etc.
To check entire functionality consider to look into index.js file. Also soon you will be able to checkout presentation of this library in https://odessafrontend.com/15/ video [Vuexi Presentation](https://www.youtube.com/watch?v=Q_olFkYVakg) also on NPMjs https://www.npmjs.com/package/vuexi.

```npm install vuexi```

## Example For Vuex actions and mutations 

```javascript
// some random vuex module for view with instances
import * as InstancesClient from '@/resources/instances'
/*
export function getInstances({ projectId }) {
  return HTTP.get(`/projects/${projectId}/instances`)
}
export function createInstance({ projectId, ...options }) {
  return HTTP.post(`/projects/${projectId}/instances`, options)
}
*/
import { begin, success, error, query, poll } from 'vuexi'

const QUERY_INSTANCES = 'QUERY_INSTANCES'
const QUERY_INSTANCES_SUCCESS = 'QUERY_INSTANCES_SUCCESS'
const QUERY_INSTANCES_FAILURE = 'QUERY_INSTANCES_FAILURE'

export default {
  namespaced: true,
  state: {
    instances: [],
    isQueryInstancesLoading: false,
    queryInstancesError: null
  },
  actions: {
    // InstancesClient.getInstances is a axious call
    queryInstances: query(QUERY_INSTANCES, InstancesClient.getInstances), // used for initial load of state
    pollInstances: poll(QUERY_INSTANCES, InstancesClient.getInstances), // used for polling purposes with setInterval
  },
  mutations: {
    [QUERY_INSTANCES]: begin('isQueryInstancesLoading', 'queryInstancesError'), // essentialy is a macros to set isQueryInstancesLoading to true and queryInstancesError to null
    [QUERY_INSTANCES_SUCCESS]: success('isQueryInstancesLoading', 'instances'), // sets isQueryInstancesLoading to false and instances from axios data object provided to this mutation from "query" or "poll" helper
    // success call can be replaces with any other to modify state on your purpose (removeById, push, silence, etc)
    [QUERY_INSTANCES_FAILURE]: error('isQueryInstancesLoading', 'queryInstancesError') // sets isQueryInstancesLoading to false and queryInstancesError to error from axious call provided to this mutation
  }
}
```

## Example for router
```javascript
import { route } from 'vuexi'

export default [
  route('/', Dashboard, {
    beforeEnter: onUnauthorizedRedirectToLogin,
    children: [
      route('', Default, { name: 'default' }),
      route('user/', UserDashboard, {
        children: [
          route('profile', UserProfile, { name: 'user-profile' }),
          route('', undefined, { name: 'default-user-profile', redirect: { name: 'user-profile' } })
        ]
      }),
      route('project/:projectId', TeamDashboard, {
        children: [
          route('', undefined, { name: 'resources', redirect: to => `/project/${to.params.projectId}/users` }),
          route('users', Users, { name: 'users' }),
          route('users/new', NewUser, { name: 'new-user' }),
          route('users/:id/edit', EditUser, { name: 'edit-user' }),
```

## Sponsors
Vuexi used by [ScalableSpace](https://scalablespace.net) and by [ScaleChamp](https://scalechamp.com)

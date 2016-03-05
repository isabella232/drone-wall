# drone-wall

The Drone Wall is a wall display component for [Drone CI server](https://github.com/drone/drone) v0.4.0.

<img src="http://tathanen.github.io/drone-wall-light.png" width="440" alt="Light theme">
<img src="http://tathanen.github.io/drone-wall-dark.png" width="440" alt="Light theme">

## Configuration

Drone Wall uses [Grunt](http://gruntjs.com/) to construct an entirely client-side web application in the `/build` directory.  You can pass several optional flags to pre-configure the application like so:

````
grunt --env=<env> --apiroot=<path> --token=<token> --theme=<theme>
````

* **env**: `[local|dev|staging|prod]` Defaults to `local`.  Selects which group of configuration variables in `env.json` are used for further tasks.  Implicitly enables mock data when set to `local`, and minifies/uglifies all scripts when set to `staging` or `prod`.

* **apiroot**: `[String]` The absolute path of where your Drone API lives.  Should be something like `https://drone.something.com/api/`.

* **token**: `[String]` A user token generated by Drone, accessible via your user profile when logged into the Drone site.  The repos and builds that appear on the Wall will be those that the owner of this token has access to in Drone.

* **theme**: `[light|dark]` Defaults to `light`.  Determines whether the Wall is displayed using the light or dark theme.

The Drone Wall requires both `apiroot` and `token` values in order to run.  If they aren't supplied via the command line build process, they will be asked for in the Wall interface itself.  This is the best way to secure your repo data from public access.  It's a good idea to pre-set `apiroot`, and require `token` to be entered via the site.  Feel free to build in the `token` value if you only plan to use the Wall internally, and aren't concerned about it being accessible to the public.

The `apiroot`, `token`, and `theme` vars can be changed via the Wall interface at any time.  Once set, they'll be stored in localstorage so you don't have to reenter them.

## Local Development

The Drone Wall requires Node.js `4.x` and npm `3.x`.

Begin with `npm install`, and then run `npm start` to build the application, start a file-watcher, and run a local server accessible at `localhost:3000`.  You can pass Grunt flags through `npm` (as of `2.0.0`) like this:

````
npm start -- -env=dev -theme=dark
````

Run `npm test` to run Angular E2E tests against mock data.

## Deployment

The Drone Wall uses [dominatr-grunt](https://github.com/vokal/dominatr-grunt) for its build process, and supports automated deployment to CloudFront/S3 on AWS.  Set an S3 bucket name and CloudFront distribution ID in your `env.json` file, and then run the following task with the appropriate `env` value:

````
grunt deploy --env=prod --aws-access-key-id=<id> --aws-secret-access-key=<key>
````

The AWS access key ID and secret access key should belong to an AWS user created via Identity and Access Management that has been granted the `AmazonS3FullAccess` and `CloudFrontFullAccess` policies.  You'll be given these values when you first create a user.

Be sure to set `apiroot`, `token`, or `theme` in the deploy command as appropriate.  Deployment should only take a few seconds, but building on AWS's end can take upwards of ten minutes.

You can also just run `grunt` with the appropriate `env` value to export the full site to your `/build` directory, and manually deploy its contents to whatever web hosting solution you prefer.

## Docker Image

We provide a lightweight (6mb) Docker image for quick setup and installation:

```
docker run -d -p 80:80 drone/drone-wall
```
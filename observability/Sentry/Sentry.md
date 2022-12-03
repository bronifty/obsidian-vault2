- [youtube dirk](https://www.youtube.com/watch?v=lI2kv3t8A0g&list=PLOwEowqdeNMr-cq9dH1AKvehShd6WfYxC&index=7)

```bash
sentry-cli releases -o <org> new -p <project> <release-version>

sentry-cli releases -o <org> -p <project> files <release-version> upload-sourcemaps <path-to-sourcemaps-folder-eg-./dist/assets>

sentry-cli sourcemaps explain c6dc6641c84f495a86d0467c37366890 -o bronifty -p react-sentry-github

sentry-cli releases -o bronifty -p react-sentry-github files react-sentry-github@1.0.0 upload-sourcemaps ./dist/assets --url-prefix '~/assets'
```
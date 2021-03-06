# Momentum Blog

This is a basic Rails 5.2 application meant to show how to use webpacker and
yarn. The base application was built using the Rails Guide tutorial and the
following steps below can be used to add webpacker/yarn functionality in
addition to adding `semantic-ui-css` and `jQuery`

### Steps to add Webpacker/Yarn

1. Add Webpacker gem to Gemfile
  - `gem 'webpacker', '~> 3.5'`
2. `bundle install`
3. `bundle exec rails webpacker:install`
4. Look over all the files that were created.
5. Change default directory from `javascript` to `client`
  - Javascript seems a little too confusing since the asset pipeline still
  has a javascript folder. Our style has been to use the name 'client'
  - `config/webpacker.yml`
  - Change from `source_path: app/javascript` to `app/client`
  - Move `app/javascript` to `app/client` (`mv app/javascript app/client`)
6. Create `app/client/packs/style.scss` for styling
7. Add javascript and style pack tag to application.html.erb
  - Javascript and Stylesheet need to have different names. I found out that
  one clobbered the other when attempting to have the same name.
  - Should be required above the 'link' and 'include' tags in case a gem needs
  to use something (like jQuery)
  - `<%= javascript_pack_tag 'application' %>`
  - `<%= stylesheet_pack_tag 'style' %>`
8. Start `bin/rails server`
9. Start `./bin/webpack-dev-server`
  - With webpacker v.3.0.0+ you can just run rails server, but I've found that
  pretty slow.
  - Alternative would be to use the foreman gem, but it makes debugging kind
  of difficult.
10. Initialize yarn `yarn init`
11. `yarn add semantic-ui-css`
  - Check to be sure it added to `package.json`
  - Add `import 'semantic-ui-css';` to `app/client/packs/application.js`
  - Add `@import '~semantic-ui-css/semantic';` to `app/client/packs/style.scss`
12. `yarn add resolve-url-loader`
  - Since `Sass/libsass` does not provide url rewriting, all linked assets must
  be relative to the output. Add the missing url rewriting using the
  resolve-url-loader.
  - More information [here](https://github.com/aganov/webpacker/blob/47eeb36970cbaaa04d4e4507e2a2bcd26e0c1e2f/docs/css.md#resolve-url-loader)
13. `yarn add jquery`
  - Add to `app/client/packs/application.js`
    * `import jQuery from 'jquery';`
    * `window.jQuery = jQuery;`
    * `window.$ = jQuery;`
15. Modify `config/webpack/environment.js`

```
const { environment } = require('@rails/webpacker');
const webpack = require('webpack');

// resolve-url-loader must be used before sass-loader
environment.loaders.get('sass').use.splice(-1, 0, {
  loader: 'resolve-url-loader'
});

// Add an ProvidePlugin
environment.plugins.prepend(
  'Provide',
  new webpack.ProvidePlugin({
    $: 'jquery',
    jQuery: 'jquery',
    jquery: 'jquery'
  })
);

const config = environment.toWebpackConfig();

module.exports = environment;

```

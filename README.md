# QuickREST

A WordPress plugin which speeds up GraphGL API requests by selective loading of plugins.

## Installation

QuickRest needs to be installed as a Must Use plugin.

## Usage

By default, the plugin will prevent any regular WordPress plugins from loading when a request is made to any GraphGL API endpoint.

Plugins can be enabled on a per-namespace basis by filtering `quickgraphql_plugin_map`, for example:

```
function my_plugin_map_function( $map ) {
  $new_map = [
    'someplugin' => [ 'some-plugin/some-plugin.php' ],
    'wp'         => [ 'plugin-one/plugin-one.php', 'plugin-two/plugin-two.php' ]
   ];
  return array_merge_recursive( $map, $new_map );
}
add_filter( 'quickgraphql_plugin_map', 'my_plugin_map_function', 15, 1 );
```

If nothing exists within the array for the current namespace, the plugin will fallback to the `_default` element which you can choose to enable.

If you want the default (or any other plugin) to load all active plugins, you can do this:

```
add_filter(
  'quickgraphql_plugin_map',
  function( $map ) {
    // Remove our filter so we don't get stuck in a loop when getting the active_plugins option.
    remove_filter( 'option_active_plugins', 'quickgraphql_filter_plugins', PHP_INT_MAX - 1 );
    $new_map = [
      '_default' => get_option( 'active_plugins' ),
    ];
    add_filter( 'option_active_plugins', 'quickgraphql_filter_plugins', PHP_INT_MAX - 1 );
    return array_merge_recursive( $map, $new_map );
  },
  10,
  1
);
```

---
Built by the team at [Delicious Media](https://www.deliciousmedia.co.uk/), a specialist WordPress development agency based in Sheffield, UK.

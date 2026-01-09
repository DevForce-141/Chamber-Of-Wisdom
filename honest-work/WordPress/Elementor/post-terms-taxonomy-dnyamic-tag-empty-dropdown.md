Often when working with posts and you select ``Post Terms`` as the source for dynamic tag, the resulting ``Taxonomy``
dropdown is empty in a glitchy manner. It seems like there are options in markup but they're empty.

There are two possible fixes:

1. Save the Post/Page and reload. The dropdown options will become available.
2. The problem was that Elementor only fetches taxonomies if the ``show_in_nav_menus setting`` is set to true.
   You can enable this setting for your taxonomies, and they will appear in your dropdown without issue. 
   If you prefer not to enable this setting, you can use the following simple PHP snippet:
    ```php
    add_filter( 'elementor_pro/dynamic_tags/post_terms/taxonomy_args', function( $args ) {
    unset( $args['show_in_nav_menus'] );
    return $args;
    }, 9999 );
    ```
   > Source: [Elementor GitHub Issue](https://github.com/elementor/elementor/issues/24663#issuecomment-2439758868)

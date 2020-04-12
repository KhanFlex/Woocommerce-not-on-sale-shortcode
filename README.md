# Woocommerce-not-on-sale-shortcode

```sh
function featured_category_product_function( $atts ) {

    global $woocommerce_loop;

    extract(shortcode_atts(array(
        'category'      => '',
        'per_page'  => '20',
        'columns'   => '4',
        'orderby' => 'date',
        'order' => 'desc'
    ), $atts));

    $product_ids_on_sale = wc_get_product_ids_on_sale();

    $args = array(
        'post_type' => 'product',
        'post_status' => 'publish',
        'ignore_sticky_posts'   => 1,
        'posts_per_page' => $per_page,
        'orderby' => $orderby,
        'order' => $order,
        'meta_query' => array(
            'key'     => '_sale_price',
            'value'   => 0,
            'compare' => '=',
            'type'    => 'NUMERIC',
        ),
        'post__not_in' => $product_ids_on_sale,
    );

    ob_start();

    $products = new WP_Query( $args );

    error_log(print_r($products, TRUE));

    $woocommerce_loop['columns'] = $columns;

    if ( $products->have_posts() ) : ?>     

        <?php woocommerce_product_loop_start(); ?>

            <?php while ( $products->have_posts() ) : $products->the_post(); ?>

                <?php wc_get_template_part( 'content', 'product' ); ?>

            <?php endwhile; // end of the loop. ?>

        <?php woocommerce_product_loop_end(); ?>

    <?php endif;

    wp_reset_postdata();

    return '<div class="woocommerce">' . ob_get_clean() . '</div>';
}

add_shortcode( 'featured_category_product', 'featured_category_product_function' );
```

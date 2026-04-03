# functions.php

<?php
// Kiểm tra để tránh truy cập trực tiếp file
if (!defined('ABSPATH')) exit;

add_filter('intermediate_image_sizes_advanced', function ($sizes) {
    // Nếu không phải upload từ box Featured Image thì dẹp hết các size cắt
    if (isset($_REQUEST['context']) && $_REQUEST['context'] !== 'featured-image') {
        return []; 
    }
    return $sizes;
});
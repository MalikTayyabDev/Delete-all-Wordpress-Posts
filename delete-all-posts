<?php
/**
 * Temporary WordPress Bulk Post Delete Script
 * Deletes normal WordPress posts in automatic batches.
 * Delete this file immediately after use.
 */

ini_set('display_errors', 1);
error_reporting(E_ALL);
set_time_limit(0);

// ======================================
// CHANGE THIS SECRET KEY BEFORE RUNNING
// ======================================
$secret_key = 'CHANGE_THIS_SECRET_123456';

// Check secret key from URL
if (!isset($_GET['key']) || $_GET['key'] !== $secret_key) {
    die('Access denied. Invalid secret key.');
}

// Load WordPress
$wp_load = __DIR__ . '/wp-load.php';

if (!file_exists($wp_load)) {
    die('wp-load.php not found. Make sure this file is placed in the WordPress root directory.');
}

require_once $wp_load;

// Batch size
$batch_size = isset($_GET['batch']) ? intval($_GET['batch']) : 300;
$batch_size = max(50, min($batch_size, 1000));

// Get posts in batches
$post_ids = get_posts([
    'post_type'              => 'post',
    'post_status'            => 'any',
    'posts_per_page'         => $batch_size,
    'fields'                 => 'ids',
    'orderby'                => 'ID',
    'order'                  => 'ASC',
    'no_found_rows'          => true,
    'ignore_sticky_posts'    => true,
    'update_post_meta_cache' => false,
    'update_post_term_cache' => false,
]);

$deleted = 0;

if (!empty($post_ids)) {
    foreach ($post_ids as $post_id) {
        // true = permanently delete, bypass Trash
        $result = wp_delete_post($post_id, true);

        if ($result) {
            $deleted++;
        }
    }
}

// Count remaining posts
$remaining = wp_count_posts('post');
$total_remaining = 0;

foreach ((array) $remaining as $status => $count) {
    $total_remaining += intval($count);
}

// Next URL for automatic batch continuation
$next_url = add_query_arg([
    'key'   => $secret_key,
    'batch' => $batch_size,
], site_url('/delete-all-posts.php'));

?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Deleting WordPress Posts</title>

    <?php if ($total_remaining > 0 && $deleted > 0) : ?>
        <meta http-equiv="refresh" content="1;url=<?php echo esc_url($next_url); ?>">
    <?php endif; ?>

    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 40px;
            background: #f6f7f7;
            color: #111;
        }

        .box {
            background: #fff;
            padding: 25px;
            max-width: 650px;
            border-radius: 8px;
            box-shadow: 0 5px 25px rgba(0,0,0,0.08);
        }

        h2 {
            margin-top: 0;
        }

        .danger {
            color: #b00020;
            font-weight: bold;
        }

        .success {
            color: #008000;
            font-weight: bold;
        }

        .progress {
            margin-top: 15px;
            padding: 12px;
            background: #f1f1f1;
            border-radius: 6px;
        }
    </style>
</head>
<body>
<div class="box">
    <h2>WordPress Bulk Post Delete</h2>

    <div class="progress">
        <p>Deleted in this batch: <strong><?php echo esc_html($deleted); ?></strong></p>
        <p>Remaining posts: <strong><?php echo esc_html($total_remaining); ?></strong></p>
        <p>Batch size: <strong><?php echo esc_html($batch_size); ?></strong></p>
    </div>

    <?php if ($total_remaining > 0 && $deleted > 0) : ?>

        <p class="danger">Deleting next batch automatically...</p>

        <script>
            setTimeout(function () {
                window.location.href = "<?php echo esc_url($next_url); ?>";
            }, 1000);
        </script>

    <?php else : ?>

        <p class="success">Finished. All normal WordPress posts have been deleted.</p>
        <p class="danger">Important: delete this file from the server immediately:</p>
        <code>delete-all-posts.php</code>

    <?php endif; ?>
</div>
</body>
</html>

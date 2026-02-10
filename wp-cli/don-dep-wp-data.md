## wp-app/wp-config.php

/* Add any custom values between this line and the "stop editing" line. */

/** Tắt Strict Mode bằng cách can thiệp trực tiếp vào kết nối DB */
if (defined('DB_NAME')) {
	$temp_conn = mysqli_connect(DB_HOST, DB_USER, DB_PASSWORD, DB_NAME);
	if ($temp_conn) {
		mysqli_query($temp_conn, "SET GLOBAL sql_mode = 'ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'");
		mysqli_query($temp_conn, "SET SESSION sql_mode = 'ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'");
		mysqli_close($temp_conn);
	}
}
// wp db optimize
// wp db size --tables --human-readable
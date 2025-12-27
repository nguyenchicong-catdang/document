# test pass
$user = \App\Models\LearnApi::first();

Hash::check('12345', $user->password);


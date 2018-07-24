# Simple RequestCriteria

## install
```
composer require zeng407/l5-repository
```

## filtering

give serchable columns like this

```
class UserRepository extedns BaseRepository
{
	protected $fieldSearchable = [
		'user_name' => [
			'column' => 'name'
			// default column value is 'user_name' if you don't specify column name
		],
		'age' => [
			'operators' => ['=','in','>=']
			//limit operators input
		],
		'gender',
		'post_title' => [
			'relation' => 'posts', // give the relation name in User Model
			'column' => 'title'
		],
		'phone_number' => [
			'relation' => 'profile.phones'
		]
	];
}

```

then you can do these query


	localhost/users?user_name_like="john"
	// select * from users where name like '%john%'
	
	localhost/users?age_gte=20
	// select * from users where age >= 20
	
	localhost/users?gender=female
	// select * from users where gender = 'female'
	
	localhost/users?age_in[]=20&age_in[]=30age_in[]=35
	// select * from users where age in (20,30,35)
	
	localhost/users?post_title_like='announcement'
	// select * from users where exists
	//  (select * from posts where posts.user_id = users.id 
	//.   and posts.title like '% announcement%')

the operations are defined at

```
class RequestCriteria implements CriteriaInterface {

protected $operatorMap = [
        'gt' => '>',
        'gte' => '>=',
        'lt' => '<',
        'lte' => '<=',
        'like' => 'like',
        'in' => 'in'
    ];

}
```

## sorting

```
class UserRepository extedns BaseRepository
{
	protected $sortable = [
		'user_name' => [
			'column' => 'name'
		],
		'post_title' => [
			'join' => 'posts',
			'column' => 'title'
		],
		'phone_number' => [
			'relation' => 'profiles.phones'
			//give the table name, not the relation name in User model
		]
	];
}
```

query

```
localhost/users?order_by=user_name
// select * from users order by name

localhost/users?order_by=phone_number&order_dir=desc (default order_dir=asc)
// select * form users 
// left join profiles on profiles.user_id = users.id
// join phones on phones.profile_id = profiles.id
// order by phone_number asec
```

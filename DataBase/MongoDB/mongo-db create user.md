```
db.createUser(
{
	user: "sebastien",

	pwd: "1234",

	roles:[{role: "readWrite",db:"test" }]})
```

# Create Admin user

use admin db.createUser( { user: "sebastien", pwd: "1234", roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] } )
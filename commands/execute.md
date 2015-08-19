# execute command

(todo)

## Execute

```JSON
{
	"document": "Empty contract",
	"execute": {
		"0": {"snapshot": { }},
		"1": {
			"update": { 
				"title": "Sample Contract",
				"conditions": "pay upon delivery"}},
		"2": {"send_email": {"to": "test@localhost.com"}}
	}
}
```

## Summary

The execute command allows you to run multiple commands in sequence.

The next command, [notary](notary.md) allows us to execute a list of commands.

# execute command

(todo)

## Execute

```JSON
{
		"document": "hello world",
		"execute": [
			{"snapshot": {}}, 
			{"update": { "terms": "agree to price" }}, 
			{"update": { "conditions": "pay upon delivery"}},
		]
	}
```

## Summary

The execute command allows you to run multiple commands in sequence.

The next command, [notary](notary.md) allows us to execute a list of commands.

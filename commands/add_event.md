# add_event command

(todo)

# Execute

```JSON
	{
		"document" : "My contract title.",
		"add_event" : {
			"type" : "chainscript/check-datetime",
			"conditions" : { 
				"expires_in_minutes_from_now" : -15
			},
			"execute" : [
				{"update" : "My contract title: EXPIRED!"},
				{"snapshot" : {}},
				{"send_email" : {"to" : "caetano@gmail.com"}}
			]
		},
		"x_chainscript" : {
			"disable_command_log" : true,
			"disable_revision_history" : true
		}
	}
```

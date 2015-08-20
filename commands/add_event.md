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
				"0": {"update" : "My contract title: EXPIRED!"},
				"1": {"snapshot" : {}},
				"2": {"send_email" : {"to" : "caetano@gmail.com"}}
			]
		},
		"x_chainscript" : {
			"disable_command_log" : true,
			"disable_revision_history" : true
		}
	}
```

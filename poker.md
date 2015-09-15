# Chainscript Poker
By combining cryptography and digital assets, a secure poker game can be implemented in Chainscript.

## Poker Agent
An agent can be created to implement specific commands related to poker game logic on top of the official chainscript agent.  Players would arrange game play between the clients.  Game play is then verified through the agent and published as a snapshot.  The snapshot serves as the central point of official game play.

## Cards
A single card is represented by its suit and value:

- "C" Clubs
- "D" Diamonds
- "H" Hearts
- "S" Spades
- "A" Ace
- "K" King
- "Q" Queen
- "J" Jack
- [2-9] Number Cards

Some examples of cards:

- "AC" Ace of Clubs
- "2J" Two of Jacks

A deck of cards, or a hand, is represented by a comma deliminted string:

```
{
	"deck" : "2C,3C,4C,5C,6C,7C,8C,9C,JC,QC,KC,AC,2D,3D,4D,5D,6D,7D,8D,9D,JD,QD,KD,AK...",
	"hands" : "4C,8H,AH,QS,3D",
	"flush" : "4C,3C,9C,KC,AC",
	"straight" : "7D,8D,9D,JD,QD"
}
```


## Open a Table
Players join a table by providing their public keys.  Public keys are needed by the Dealer Agent to issue a secured deck of cards

```JSON
{
	"open_table" : {
		"title" : "Cryptographic World Series of Poker",
		"game" : "poker/5-card-draw",
		"bet_min" : 2,
		"bet_max" : 6,
		"players" : {
			"jack1" : "03cb972b336c9d1b675db37fcdc9a473dac18987928ce893bbfd29649f9bbeec56",
			"mary007" : "cdc9a473dac18987928ce893bbfd29649f9bbeec5603cb972b336c9d1b675db37f",
			"thedude" : "2b336c9d1b675db37fcdc9a473dac189879203cb978ce893bbfd29649f9bbeec56",
			"heyjesus" : "a473dac18987928ce893b03cb972b336c9d1b675db37fcdc9bfd29649f9bbeec56",
		}
	}
}
```

The dealer agent then publishes a table with deposit addresses for the players.  The corresponding withdraw address is calculated based on the public key provided by the player.  

A deposit\_address is created as a way for a player to place their money on the table.  Players must deposit some coins into their address to play.  

They player has a limited time to deposit.  If not deposited, the table is cancelled and all coins returned to the withdraw_addresses.

```JSON

{
  "body": {
    "content": {
      "title": "Cryptographic World Series of Poker",
      "game": "poker/5-card-draw",
	  "bet_min" : 2,
	  "bet_max" : 6,      
      "players": {
        "0": {
          "nickname": "jack1",
          "withdraw_address": "mtSZsLDiseag9BL1J8Pmvw1qK4XZSgicQk",
          "public_key": "03cb972b336c9d1b675db37fcdc9a473dac18987928ce893bbfd29649f9bbeec56",
          "deposit_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw"
        },
        "1": {
          "nickname": "mary007",
          "withdraw_address": "mL1J8Pmvw1qK4XZSgictSZsLDiseag9BQk",
          "public_key": "cdc9a473dac18987928ce893bbfd29649f9bbeec5603cb972b336c9d1b675db37f",
          "deposit_address": "msLDiseag9BL1J8Pmvwt1qK4XZSgicQkSZ"
        },
        "2": {
          "nickname": "thedude",
          "withdraw_address": "mag9BL1J8Pmvw1qK4XZtSZsLDiseSgicQk",
          "public_key": "2b336c9d1b675db37fcdc9a473dac189879203cb978ce893bbfd29649f9bbeec56",
          "deposit_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw"
        },
        "3": {
          "nickname": "heyjesus",
          "withdraw_address": "mseag9BL1J8Pmvw1qtSZsLDiK4XZSgicQk",
          "public_key": "a473dac18987928ce893b03cb972b336c9d1b675db37fcdc9bfd29649f9bbeec56",
          "deposit_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw"
        }
      }
    },
    "x_meta": {
      "current_state": "open",
      "workflow": {
        "open": "funded,closed",
        "funded": "ante,closed",
        "ante": "deal,closed",
        "deal": "bet,ante",
        "bet": "draw,awarded",
        "draw": "bet,awarded",
        "awarded" : "ante,closed"
      }
    }
  },
  "x_chainscript": {
    "digest": "e79a0b10ae1cc38cc4014ec3427b75cc11648d6c"
  }
}
```

Once all players have placed their money on the table, the game state will change to 'funded'.

```JSON
{
  "body": {
    "content": {
      "title": "Cryptographic World Series of Poker",
      "game": "poker/5-card-draw",
	  "bet_min" : 2,
	  "bet_max" : 6,      
      "players": {...}
    },
    "x_meta": {
      "current_state": "funded"
    }
  }
}
```

## Starting a Round
Once in the funded state, the first two players are required to place an ante:

```JSON
{
	"shuffle" : {}
}
```

The players are then instructed by the following keys 'current\_player' & 'min\_bet'

```JSON
{
	"players": {
		"0": {
	    	"nickname": "jack1",
	      	"withdraw_address": "mtSZsLDiseag9BL1J8Pmvw1qK4XZSgicQk",
	      	"public_key": "03cb972b336c9d1b675db37fcdc9a473dac18987928ce893bbfd29649f9bbeec56",
	      	"deposit_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw",
	      	"bet" : 0,
	      	"hand" : ""
	    }
	},
	"game" : {
		"round" : 1,
		"pot" : 0,
		"description" : "Deposit Ante",
		"current_player" : 0,
		"min_bet" : 1
	},
	"x_meta": {
		"current_state" : "ante"
	}
}
```

Player "0" must then issue a command with their signature included:

```JSON
{
	"ante" : 1
}
```

Player "1" must then issue the second ante;

```JSON
{
	"ante" : 2
}
```

After each call, the Dealer Agent published a valid snapshot.

## Deal
With the first two players ante'd up the dealer shuffles and deals out the cards:

```JSON
{
	"players": {
		"0": {
	    	"nickname": "jack1",
	      	"withdraw_address": "mtSZsLDiseag9BL1J8Pmvw1qK4XZSgicQk",
	      	"public_key": "03cb972b336c9d1b675db37fcdc9a473dac18987928ce893bbfd29649f9bbeec56",
	      	"deposit_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw",
	      	"bet" : 1,
	      	"hand" : "YXNkZnNdDQphc2RmDQphc2Q"
	    },
	    "1": {
          	"nickname": "mary007",
          	"withdraw_address": "mL1J8Pmvw1qK4XZSgictSZsLDiseag9BQk",
          	"public_key": "cdc9a473dac18987928ce893bbfd29649f9bbeec5603cb972b336c9d1b675db37f",
          	"deposit_address": "msLDiseag9BL1J8Pmvwt1qK4XZSgicQkSZ"
	      	"bet" : 2,
	      	"hand" : "dDQphc2RmDQpYXNkZnNhc2Q"
        },
        "2": {
          "nickname": "thedude",
          "withdraw_address": "mag9BL1J8Pmvw1qK4XZtSZsLDiseSgicQk",
          "public_key": "2b336c9d1b675db37fcdc9a473dac189879203cb978ce893bbfd29649f9bbeec56",
          "deposit_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw"
	      "bet" : 0,
	      "hand" : "QpYXNkZnNdDQphc2RmDhc2Q"
        },
        "3": {
          "nickname": "heyjesus",
          "withdraw_address": "mseag9BL1J8Pmvw1qtSZsLDiK4XZSgicQk",
          "public_key": "a473dac18987928ce893b03cb972b336c9d1b675db37fcdc9bfd29649f9bbeec56",
          "deposit_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw"
	      "bet" : 0,
	      "hand" : "DQpYXNkZndDQphc2RmNhc2Q"
        }        
	},
	"game" : {
		"round" : 1,
		"pot_amount" : 3,
		"description" : "Player Check or Bet?",
		"current_player" : 2,
		"min_bet" : 0,
		"deck" : "YXNkZnNdDQphc2RmDQphc2QNCmFzZA0KZmFzZA0KZmFkcw0KZnNhZY...",
		"deck_index" : 20
	},
	"x_meta": {
		"current_state" : "bet"
	}	
}
```

The game's deck is encrypted with the dealer's private key.  Keys are issued for a single chainscript/table and used to secure the deck.  Cards issued during the draw phase are pulled from the encrypted deck.  At the end of each round the remaining cards revealed.

The top card from the deck is represented byt the "deck_index" value.  Each time a card is pulled from the deck, the index is incremented.  

5 cards are issued to each player.  Each player's hand is encrypted with their public key allowing them to view their hands in privacy.  

## First Betting Round
After the ante, the players are requested to place their bets:

```JSON
	"game" : {
		"round" : 1,
		"pot_amount" : 3,
		"description" : "Player Check or Bet?",
		"current_player" : 2,
		"min_bet" : 0,
		"deck" : "YXNkZnNdDQphc2RmDQphc2QNCmFzZA0KZmFzZA0KZmFkcw0KZnNhZY...",
		"deck_index" : 20
	},
	"x_meta": {
		"current_state" : "bet"
	}
```

Player 2 is required to respond with any of the following commands:

```JSON
{
	"fold" : {},
	"call" : {},
	"bet" : 2
}
```

Players continue until the table has completed the betting round.  Then players are asked to draw.

```JSON
{
	"players": {
		"0": {
	      "nickname": "jack1",
	      "bet" : 2
	    },
	    "1": {
          "nickname": "mary007",
	      "bet" : 2,
        },
        "2": {
          "nickname": "thedude",
	      "bet" : 2
        },
        "3": {
          "nickname": "heyjesus",
	      "bet" : 2
        }        
	},
	"game" : {
		"round" : 1,
		"pot_amount" : 6,
		"description" : "Player draw?",
		"current_player" : 2,
		"min_bet" : 0,
		"deck" : "YXNkZnNdDQphc2RmDQphc2QNCmFzZA0KZmFzZA0KZmFkcw0KZnNhZY..."
	},
	"x_meta": {
		"current_state" : "draw"
	}
}
```

## Draw
Each player is given the chance to choose which cards are kept:

```JSON
{
	"keep" : "AS,KS,QS"
}
```

To protect the player's privacy, SSL end-to-end encryption is assumed.  
The dealer agent then pull's cards from the encrypted deck to complete the player's hand:

```JSON
{
	"players": {
        "2": {
          "nickname": "thedude",
	      "hand" : "NkZndDQphcDQpYX2RmNhc2Q",
	      "discarded" : "DQpYX2RmN"
        }
	},
	"game" : {
		"round" : 1,
		"pot_amount" : 6,
		"description" : "Player draw?",
		"current_player" : 3,
		"min_bet" : 0,
		"deck" : "YXNkZnNdDQphc2RmDQphc2QNCmFzZA0KZmFzZA0KZmFkcw0KZnNhZY...",
		"deck_index" : 22
	},
	"x_meta": {
		"current_state" : "draw"
	}
}
```

The discarded cards are encrypted by the dealer and stored with the player for auditing purposes.

Each player is given a change to keep their selected cards.

## Final Betting Round

For the final round of betting, each player is asked to submit an action:

```JSON
{
	"fold" : {},
	"call" : {},
	"bet" : 2
}
```
If a raise is made, the other players are asked to bet or fold.

## Award
After the play player's bet the results are published:

```JSON
{
	"players": {
		"0": {
	    	"nickname": "jack1",
	      	"award" : 20
	    },
	    "1": {
          	"nickname": "mary007",
        },
        "2": {
          "nickname": "thedude",
        },
        "3": {
          "nickname": "heyjesus",
        }        
	},
	"game" : {
		"round" : 1,
		"pot_amount" : 30,
		"description" : "Player 0 won 20 units",
		"deck" : "YXNkZnNdDQphc2RmDQphc2QNCmFzZA0KZmFzZA0KZmFkcw0KZnNhZY...",
		"deck_index" : 29,
		"deck_remaining" : "4D,8D,2C,AS,9C,3D,JH,3D,9C,7S..."
	},
	"x_meta": {
		"current_state" : "awarded"
	}	
}
```

The dealer awards the player by depositing the pot into to player's withdraw address.

To play another round, any player can issue the "start_round" command:

```JSON
{
	"shuffle" : {}
}
```

The game repeats...







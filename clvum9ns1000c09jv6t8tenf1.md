---
title: "Rock, paper, scissor in python"
datePublished: Mon May 06 2024 07:04:45 GMT+0000 (Coordinated Universal Time)
cuid: clvum9ns1000c09jv6t8tenf1
slug: rock-paper-scissor-in-python

---

```python
import random

def get_player_choice():
    choices = ['rock', 'paper', 'scissors']
    player_choice = input("Choose rock, paper, or scissors: ").lower()
    while player_choice not in choices:
        player_choice = input("Invalid choice. Please choose rock, paper, or scissors: ").lower()
    return player_choice

def get_computer_choice():
    choices = ['rock', 'paper', 'scissors']
    computer_choice = random.choice(choices)
    return computer_choice

def determine_winner(player_choice, computer_choice):
    if player_choice == computer_choice:
        return "It's a tie!"
    elif (player_choice == 'rock' and computer_choice == 'scissors') or \
         (player_choice == 'paper' and computer_choice == 'rock') or \
         (player_choice == 'scissors' and computer_choice == 'paper'):
        return "You win!"
    else:
        return "Computer wins!"

def play_game():
    print("Welcome to Rock-Paper-Scissors!")
    player_choice = get_player_choice()
    computer_choice = get_computer_choice()
    print(f"You chose {player_choice}.")
    print(f"The computer chose {computer_choice}.")
    result = determine_winner(player_choice, computer_choice)
    print(result)

play_game()
```
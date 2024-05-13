# Hangman Game

## Introduction
### What is your application?
This is a Python Hangman game application. Hangman is a word guessing game where the player has to guess the hidden word letter by letter within a certain number of attempts.
### How to run the program?
To run the program, execute the `hangman.py` file in a Python environment.
### How to use the program?
1. Upon running the program, a word will be randomly selected from the provided word list.
2. The player needs to guess the letters of the word one by one.
3. Enter a single letter as your guess.
4. The program will inform you if your guess is correct or not and display the current status of the word.
5. Continue guessing until you either guess the entire word or run out of attempts.
## Body/Analysis
### Object-Oriented Programming Pillars
### 1. Encapsulation
- Encapsulation is demonstrated through the use of classes and methods to encapsulate data (attributes) and behavior (methods) within each class.
- For example, the `HangmanGame` class encapsulates data such as the word to guess, maximum attempts, and player guesses, along with methods to manipulate and interact with this data.

### 2. Inheritance
- Inheritance is utilized to create specialized versions of the `HangmanGame` class.
- The `SinglePlayerHangmanGame` class inherits from the `HangmanGame` class, adding additional functionality specific to single-player games such as hint usage.

### 3. Polymorphism
- Polymorphism is demonstrated through method overriding in subclasses.
- For example, the `get_masked_word()` method is overridden in the `SinglePlayerHangmanGame` class to implement hint functionality.

### 4. Abstraction
- Abstraction is achieved by hiding the implementation details of methods from the user.
- Users interact with the game through high-level methods such as `guess()` and `display_game_status()`, without needing to know the internal workings of these methods.

#### Design patterns
### 1. Factory Method Pattern
- The Factory Method Pattern is implemented through the `HangmanGameFactory` class, which provides a method for creating instances of `HangmanGame` objects.
- This pattern allows for the creation of game objects without specifying the exact class of object that will be created.

```python
class HangmanGameFactory:
    _instance = None

    @staticmethod
    def get_instance():
        if HangmanGameFactory._instance is None:
            HangmanGameFactory._instance = HangmanGameFactory()
        return HangmanGameFactory._instance

    def create_game(self):
        return HangmanGameBuilder().build()

```
The HangmanGameFactory class encapsulates the logic for creating instances of HangmanGame objects. It provides a single method create_game() that returns a new instance of HangmanGame.

### 2. Builder Pattern
The Builder Pattern is employed with the HangmanGameBuilder class, which constructs HangmanGame objects.
It allows for the construction of game objects with different configurations (e.g., word and maximum attempts) in a step-by-step manner.

```python
class HangmanGameBuilder:
    def __init__(self):
        self.word = HangmanWordProvider.get_word()
        self.max_attempts = 6

    def set_word(self, word):
        self.word = word
        return self

    def set_max_attempts(self, max_attempts):
        self.max_attempts = max_attempts
        return self

    def build(self):
        return HangmanGame(self.word, self.max_attempts)
```
The HangmanGameBuilder class allows for the construction of HangmanGame objects with different configurations. It provides methods to set the word and maximum attempts, and a build() method to create the game object with the specified settings.

### 3. Singleton Pattern
The Singleton Pattern is utilized in the HangmanGameFactory class to ensure that only one instance of the factory is created throughout the program's execution.
This ensures that the same factory instance is used consistently to create game objects.

```python
class HangmanGameFactory:
    _instance = None

    @staticmethod
    def get_instance():
        if HangmanGameFactory._instance is None:
            HangmanGameFactory._instance = HangmanGameFactory()
        return HangmanGameFactory._instance

```
### 4. Strategy Pattern
The Strategy Pattern is implicitly used in the design of the game logic, allowing for different strategies for selecting words and determining game outcomes.
For example, the HangmanWordProvider class provides a strategy for selecting random words, while the HangmanGame class defines strategies for checking game over and win conditions.
```python 
class HangmanWordProvider:
    @staticmethod
    def get_word():
        with open("words.txt", "r") as file:
            words = file.readlines()
        return random.choice([word.strip().lower() for word in words])
```
The HangmanWordProvider class encapsulates the strategy for selecting words for the game. It provides a method get_word() which selects a random word from a file containing a list of words.

### Reading from a File
The provided code reads from a file to obtain a list of words for the Hangman game. Here's how it works:

1.The HangmanWordProvider class contains a static method get_word() that reads from a file named "words.txt".

2.The file is opened in read mode using a context manager (with open("words.txt", "r") as file:).

3.The readlines() method is used to read all lines from the file into a list.

4.The method returns a randomly chosen word from the list after stripping any leading or trailing whitespace.

```pyhton

class HangmanWordProvider:
    @staticmethod
    def get_word():
        with open("words.txt", "r") as file:
            words = file.readlines()
        return random.choice([word.strip().lower() for word in words])
```
### Functions for Importing and Exporting Data
To implement functions for importing and exporting data (saving results, state, output, or progress of the program), we can add functions to save and load game state using JSON format.

#### Saving Game State:
```python
import json

def save_game_state(game, filename):
    with open(filename, 'w') as file:
        json.dump({'word': game.word, 'max_attempts': game.max_attempts, 'guesses': game.guesses}, file)
```

This function takes the current game object and a filename as input. It then writes the game state (word, max_attempts, and guesses) to the specified file in JSON format.

#### Loading Game State:
```python
def load_game_state(filename):
    with open(filename, 'r') as file:
        data = json.load(file)
        return HangmanGame(data['word'], data['max_attempts']), data['guesses']
```


This function loads the game state from a JSON file specified by the filename parameter. It reads the JSON data from the file, creates a new HangmanGame object with the word and max_attempts, and returns it along with the guesses.

Example Usage:
```python
# Save game state
save_game_state(game, 'hangman_state.json')

# Load game state
loaded_game, loaded_guesses = load_game_state('hangman_state.json')
```
These functions provide a convenient way to save and load the state of the Hangman game, allowing players to resume their progress at a later time.

### Testing
Step 1: Test Guessing Correct Letters
```python
import unittest
from hangman import HangmanGame

class TestHangmanGame(unittest.TestCase):
    def test_guess_correct_letter(self):
        # Create a HangmanGame instance with a word "test"
        game = HangmanGame("test")
        game.guess("t")
        # Check if the masked word is updated correctly
        self.assertEqual(game.get_masked_word(), "t__t")
        # Check if the game is not over yet
        self.assertFalse(game.is_game_over())
        # Check if the game is not won yet
        self.assertFalse(game.is_win())

if __name__ == "__main__":
    unittest.main()
```
Step 2: Test Guessing Incorrect Letters
```python
import unittest
from hangman import HangmanGame

class TestHangmanGame(unittest.TestCase):
    def test_guess_incorrect_letter(self):
        game = HangmanGame("test")
        #  incorrect letter 'x'
        game.guess("x")
        # max attempts are decreased
        self.assertEqual(game.max_attempts, 5)
        # masked word remains unchanged
        self.assertEqual(game.get_masked_word(), "____")
        # game is not over yet
        self.assertFalse(game.is_game_over())
        # game is not won yet
        self.assertFalse(game.is_win())

if __name__ == "__main__":
    unittest.main()
```
Step 3: Test Winning the Game
```python
import unittest
from hangman import HangmanGame

class TestHangmanGame(unittest.TestCase):
    def test_win_game(self):
        game = HangmanGame("test")
        game.guess("t")
        game.guess("e")
        game.guess("s")
        # game is won
        self.assertTrue(game.is_win())
        # game is over
        self.assertTrue(game.is_game_over())

if __name__ == "__main__":
    unittest.main()
```
These steps progressively test different aspects of the HangmanGame class, from guessing correct and incorrect letters to winning the game. You can execute each step independently to verify the correctness of your implementation at each stage.

## Hangman Game Implementation Result

The Hangman game implementation showcases a well-structured and modular design, utilizing object-oriented programming principles.


The game supports single-player gameplay, featuring a default difficulty level with a standard number of attempts per game. However, it does not currently include a mechanism for players to choose a difficulty level. This could be implemented as a future enhancement, allowing players to select from different difficulty settings, such as easy, medium, or hard, with corresponding variations in word length or maximum attempts.

The user interface, represented by the `HangmanUI` class, provides clear prompts for input and displays the game status and results effectively. Additionally, the game features functionality to save and load game states, enhancing the user experience by allowing players to resume their progress at a later time.

Overall, the provided Hangman game serves as a solid foundation for an engaging and customizable gaming experience, with ample opportunities for expansion and refinement based on specific requirements and preferences.

### Conclusion
In conclusion, while the current Hangman game provides a solid foundation, there are exciting avenues for future development. Integrating difficulty levels and multiplayer functionality could enhance gameplay diversity and social engagement. Improving the user interface through graphical enhancements and ensuring code reliability via rigorous testing are essential steps for an enriched gaming experience. By embracing these enhancements, the Hangman game can evolve into a more compelling and enjoyable experience for players of all levels.




      

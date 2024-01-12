import random
import nltk
import os

class Chatbot:
    def __init__(self):
        self.personality = {
            "greetings": ["Salut! Comment ça va?", "Bonjour, comment puis-je vous aider?"],
            "farewells": ["Au revoir! À bientôt.", "À la prochaine!"],
            "name": None
        }
        self.keywords = {
            "nom": [
                "Enchanté de faire votre connaissance! Comment puis-je vous aider?",
                "Votre nom a été enregistré. Quelle est votre question?"
            ],
            "météo": ["Il fait beau aujourd'hui, n'est-ce pas?", "La météo annonce de la pluie pour demain."],
            "sport": ["Quel est votre sport préféré?", "Moi, j'aime bien le football."],
            "musique": ["Quelle est votre musique préférée?", "Moi, j'écoute de tout."]
        }
        self.memory_file = "chatbot_memory.txt"
        self.load_memory()

    def greet(self):
        return random.choice(self.personality["greetings"])

    def farewell(self):
        return random.choice(self.personality["farewells"])

    def store_memory(self, user_input):
        with open(self.memory_file, "a", encoding="utf-8") as file:
            file.write(user_input + "\n")

    def load_memory(self):
        if os.path.exists(self.memory_file):
            with open(self.memory_file, "r", encoding="utf-8") as file:
                self.memory = file.readlines()
        else:
            self.memory = []

    def handle_name(self, user_input):
        # Traitement du nom fourni par l'utilisateur
        words = user_input.split()
        name_index = words.index("nom") + 1 if "nom" in words and len(words) > words.index("nom") else -1

        if name_index != -1 and name_index < len(words):
            self.personality["name"] = words[name_index]
            return random.choice(self.keywords["nom"])
        else:
            return "Je n'ai pas compris votre nom. Pourriez-vous le répéter?"

    def respond(self, user_input):
        self.store_memory(user_input)

        tokens = nltk.word_tokenize(user_input)
        tags = nltk.pos_tag(tokens)
        nouns = [word for word, tag in tags if tag.startswith("N")]

        for noun in nouns:
            if noun.lower() in self.keywords:
                return random.choice(self.keywords[noun.lower()])

        # Si l'utilisateur donne son nom
        if "nom" in user_input.lower():
            return self.handle_name(user_input)

        # Si l'utilisateur demande à rappeler la mémoire
        if "mémoire" in user_input.lower():
            return "Voici ce que je me souviens:\n{}".format("\n".join(self.memory))

        # Sinon, renvoie une réponse générique
        return "Je ne suis pas sûr de comprendre. Pouvez-vous reformuler?"

# Création d'une instance du chatbot
chatbot = Chatbot()

# Interaction avec l'utilisateur
print(chatbot.greet())
user_input = input("Utilisateur: ")
print("Chatbot:", chatbot.respond(user_input))
print(chatbot.farewell())

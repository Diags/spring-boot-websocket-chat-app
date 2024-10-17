# Spring Boot WebSocket Chat Application

Cette application de chat utilise **WebSocket** avec **Spring Boot** pour permettre la communication en temps réel entre les utilisateurs dans une salle de discussion. Les utilisateurs peuvent rejoindre la salle de chat en fournissant un nom d'utilisateur, envoyer des messages, et être notifiés des entrées et sorties des autres participants.

## Fonctionnalités

- **Connexion des utilisateurs** : Les utilisateurs peuvent se connecter en fournissant un nom d'utilisateur.
- **Messagerie en temps réel** : Les utilisateurs peuvent échanger des messages en temps réel grâce à WebSocket.
- **Notifications d'entrée et de sortie** : Lorsque les utilisateurs rejoignent ou quittent la salle de chat, des messages de notification sont envoyés.
- **Gestion des déconnexions** : L'application détecte lorsque les utilisateurs se déconnectent et envoie un message de sortie.
- **Utilisation de SockJS et STOMP** : L'application utilise SockJS pour établir une connexion WebSocket et STOMP pour gérer les messages.

---

## Structure de l'Application

### 1. **Backend : Spring Boot**

Le backend est construit avec **Spring Boot** et utilise **WebSocket** pour permettre une communication bidirectionnelle entre le serveur et le client.

#### a. **`ChatController.java`**

Ce contrôleur gère les messages WebSocket. Il contient deux méthodes :

- **`sendMessage()`** : Gère les messages envoyés par les utilisateurs. Il diffuse les messages à tous les abonnés du canal `/topic/public`.
- **`addUser()`** : Ajoute un utilisateur à la session WebSocket, en stockant son nom d'utilisateur dans les attributs de session et en notifiant les autres utilisateurs qu'un nouveau participant a rejoint la salle de chat.

```java
@Controller
public class ChatController {

    @MessageMapping("/chat.sendMessage")
    @SendTo("/topic/public")
    public ChatMessage sendMessage(@Payload ChatMessage chatMessage) {
        return chatMessage;
    }

    @MessageMapping("/chat.addUser")
    @SendTo("/topic/public")
    public ChatMessage addUser(@Payload ChatMessage chatMessage, SimpMessageHeaderAccessor headerAccessor) {
        headerAccessor.getSessionAttributes().put("username", chatMessage.getSender());
        return chatMessage;
    }
}

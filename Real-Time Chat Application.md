,,,ruby

// Message.java
import java.io.Serializable;
import java.util.Date;

public class Message implements Serializable {
    private static final long serialVersionUID = 1L;

    private String sender;
    private String content;
    private Date timestamp;

    public Message(String sender, String content) {
        this.sender = sender;
        this.content = content;
        this.timestamp = new Date();
    }

    // Getters and setters
    // You can add more fields like message ID, etc.
}

// Server.java
import java.io.*;
import java.net.*;
import java.util.ArrayList;
import java.util.List;

public class Server {
    private List<ClientHandler> clients = new ArrayList<>();

    public void start(int port) {
        try {
            ServerSocket serverSocket = new ServerSocket(port);
            System.out.println("Server started on port " + port);

            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("New client connected: " + clientSocket);
                ClientHandler clientHandler = new ClientHandler(clientSocket, this);
                clients.add(clientHandler);
                new Thread(clientHandler).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void broadcastMessage(Message message) {
        for (ClientHandler client : clients) {
            client.sendMessage(message);
        }
    }

    public static void main(String[] args) {
        Server server = new Server();
        server.start(12345); // Start server on port 12345
    }
}

class ClientHandler implements Runnable {
    private Socket clientSocket;
    private Server server;
    private ObjectOutputStream out;
    private ObjectInputStream in;

    public ClientHandler(Socket socket, Server server) {
        this.clientSocket = socket;
        this.server = server;
        try {
            out = new ObjectOutputStream(clientSocket.getOutputStream());
            in = new ObjectInputStream(clientSocket.getInputStream());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void run() {
        try {
            while (true) {
                Message message = (Message) in.readObject();
                server.broadcastMessage(message);
            }
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public void sendMessage(Message message) {
        try {
            out.writeObject(message);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

// Client.java
import java.io.*;
import java.net.*;

public class Client {
    private String username;

    public Client(String username) {
        this.username = username;
    }

    public void start(String serverAddress, int port) {
        try {
            Socket socket = new Socket(serverAddress, port);
            ObjectOutputStream out = new ObjectOutputStream(socket.getOutputStream());
            ObjectInputStream in = new ObjectInputStream(socket.getInputStream());

            BufferedReader userInput = new BufferedReader(new InputStreamReader(System.in));

            // Start a separate thread to listen for incoming messages from the server
            new Thread(() -> {
                try {
                    while (true) {
                        Message message = (Message) in.readObject();
                        System.out.println(message.getSender() + ": " + message.getContent());
                    }
                } catch (IOException | ClassNotFoundException e) {
                    e.printStackTrace();
                }
            }).start();

            // Main thread for sending messages to the server
            while (true) {
                System.out.print("Enter message: ");
                String content = userInput.readLine();
                Message message = new Message(username, content);
                out.writeObject(message);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        String username = args[0]; // Provide username as command line argument
        Client client = new Client(username);
        client.start("localhost", 12345); // Connect to server running on localhost and port 12345
    }
}

,,,

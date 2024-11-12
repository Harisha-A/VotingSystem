package miniproject;

import java.awt.*;
import java.sql.*;
import javax.swing.*;
import javax.swing.table.DefaultTableModel;

public class LoginRegisterApp {

    // MySQL Database credentials
    private static final String DB_URL = "jdbc:mysql://localhost:3306/voting_system";
    private static final String USER = "root";
    private static final String PASS = "PASS"; // Use your MySQL password

    // Frame for the main interface
    private static JFrame frame;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            frame = new JFrame("ONLINE VOTING SYSTEM");
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            frame.setSize(400, 300);
            showHomePage();  // Show home page when application starts
            frame.setVisible(true);
        });
    }

    // Show the home page
    private static void showHomePage() {
        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(5, 1));  // Adjust grid layout to fit the buttons

        JLabel welcomeLabel = new JLabel("Welcome to the Voting System", JLabel.CENTER);
        welcomeLabel.setFont(new Font("Arial", Font.BOLD, 18));
        panel.add(welcomeLabel);

        JButton loginButton = new JButton("Login");
        JButton registerButton = new JButton("Register");
        JButton officerLoginButton = new JButton("Officer Login");
        JButton showResultsButton = new JButton("Show Results");

        loginButton.addActionListener(e -> showLoginPanel());
        registerButton.addActionListener(e -> showRegisterPanel());
        officerLoginButton.addActionListener(e -> showOfficerLoginPanel());
        showResultsButton.addActionListener(e -> showVotingResults());

        panel.add(loginButton);
        panel.add(registerButton);
        panel.add(officerLoginButton);
        panel.add(showResultsButton);

        frame.getContentPane().removeAll();
        frame.getContentPane().add(panel);
        frame.revalidate();
        frame.repaint();
    }

    // Show the login panel
 // Show the login panel
    private static void showLoginPanel() {
        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(6, 2));  // Adjust grid to fit the new back button

        JLabel usernameLabel = new JLabel("Username:");
        JLabel passwordLabel = new JLabel("Password:");

        JTextField usernameField = new JTextField();
        JPasswordField passwordField = new JPasswordField();

        JButton loginButton = new JButton("Login");
        loginButton.addActionListener(e -> handleLogin(usernameField.getText(), new String(passwordField.getPassword())));

        // Back button to return to the home page
        JButton backButton = new JButton("Back");
        backButton.addActionListener(e -> showHomePage());

        panel.add(usernameLabel);
        panel.add(usernameField);
        panel.add(passwordLabel);
        panel.add(passwordField);
        panel.add(loginButton);
        panel.add(backButton);

        frame.getContentPane().removeAll();
        frame.getContentPane().add(panel);
        frame.revalidate();
        frame.repaint();
    }

    // Show the registration panel
 // Show the registration panel
    private static void showRegisterPanel() {
        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(6, 2)); // Adjust grid layout to fit back button

        JLabel usernameLabel = new JLabel("Username:");
        JLabel passwordLabel = new JLabel("Password:");
        JLabel confirmPasswordLabel = new JLabel("Confirm Password:");

        JTextField usernameField = new JTextField();
        JPasswordField passwordField = new JPasswordField();
        JPasswordField confirmPasswordField = new JPasswordField();

        JButton registerButton = new JButton("Register");

        registerButton.addActionListener(e -> handleRegister(
            usernameField.getText(),
            new String(passwordField.getPassword()),
            new String(confirmPasswordField.getPassword())
        ));

        // Back button to return to the home page or login page
        JButton backButton = new JButton("Back");
        backButton.addActionListener(e -> showHomePage());

        panel.add(usernameLabel);
        panel.add(usernameField);
        panel.add(passwordLabel);
        panel.add(passwordField);
        panel.add(confirmPasswordLabel);
        panel.add(confirmPasswordField);
        panel.add(registerButton);
        panel.add(backButton);

        frame.getContentPane().removeAll();
        frame.getContentPane().add(panel);
        frame.revalidate();
        frame.repaint();
    }


    // Handle user login
    public static void handleLogin(String username, String password) {
        if (isUserValid(username, password)) {
            // If the user has not voted yet, show the voting interface
            if (hasUserVoted(username)) {
                JOptionPane.showMessageDialog(frame, "You have already voted.");
            } else {
                showVotingPanel(username);
            }
        } else {
            JOptionPane.showMessageDialog(frame, "Invalid username or password.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    // Validate user login credentials from the database
    private static boolean isUserValid(String username, String password) {
        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String query = "SELECT * FROM users WHERE username = ? AND password = ?";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, username);
                statement.setString(2, password); // Hash password here for better security
                ResultSet resultSet = statement.executeQuery();
                return resultSet.next();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }

    // Check if the user has voted
    private static boolean hasUserVoted(String username) {
        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String query = "SELECT * FROM votes WHERE username = ?";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, username);
                ResultSet resultSet = statement.executeQuery();
                return resultSet.next(); // If there's a result, the user has voted
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }

    // Show the voting results
    private static void showVotingResults() {
        JPanel panel = new JPanel();
        panel.setLayout(new BorderLayout());

        JLabel resultLabel = new JLabel("Voting Results:");
        panel.add(resultLabel, BorderLayout.NORTH);

        // Retrieve results from the database
        DefaultTableModel resultTableModel = new DefaultTableModel();
        resultTableModel.addColumn("Candidate");
        resultTableModel.addColumn("Votes");

        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String query = "SELECT candidate_name, COUNT(*) AS vote_count FROM votes GROUP BY candidate_name ORDER BY vote_count DESC";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                ResultSet resultSet = statement.executeQuery();
                while (resultSet.next()) {
                    String candidate = resultSet.getString("candidate_name");
                    int voteCount = resultSet.getInt("vote_count");
                    resultTableModel.addRow(new Object[]{candidate, voteCount});
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }

        // Create a table to show results
        JTable resultTable = new JTable(resultTableModel);
        JScrollPane resultScrollPane = new JScrollPane(resultTable);
        panel.add(resultScrollPane, BorderLayout.CENTER);

        JButton backButton = new JButton("Back");
        backButton.setHorizontalAlignment(SwingConstants.RIGHT);
        backButton.addActionListener(e -> showHomePage());  // Go back to home page
        panel.add(backButton, BorderLayout.SOUTH);

        frame.getContentPane().removeAll();
        frame.getContentPane().add(panel);
        frame.revalidate();
        frame.repaint();
    }

    // Show the voting panel after login
    private static void showVotingPanel(String username) {
        JPanel panel = new JPanel();
        panel.setLayout(new BorderLayout());

        JLabel votingLabel = new JLabel("Welcome, " + username + ". Please select a candidate to vote.");
        panel.add(votingLabel, BorderLayout.NORTH);

        // Get candidates from the database
        DefaultListModel<String> candidateListModel = new DefaultListModel<>();
        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String query = "SELECT candidate_name FROM candidates";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                ResultSet resultSet = statement.executeQuery();
                while (resultSet.next()) {
                    candidateListModel.addElement(resultSet.getString("candidate_name"));
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }

        JList<String> candidateList = new JList<>(candidateListModel);
        JScrollPane candidateScrollPane = new JScrollPane(candidateList);
        panel.add(candidateScrollPane, BorderLayout.CENTER);

        JButton voteButton = new JButton("Vote");
        voteButton.addActionListener(e -> voteToCandidates(username, candidateList.getSelectedValue()));
        panel.add(voteButton, BorderLayout.SOUTH);

        frame.getContentPane().removeAll();
        frame.getContentPane().add(panel);
        frame.revalidate();
        frame.repaint();
    }

    // Method to handle voting to the selected candidate
    private static void voteToCandidates(String username, String selectedCandidate) {
        if (selectedCandidate == null) {
            JOptionPane.showMessageDialog(frame, "Please select a candidate to vote.", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            // Check if the user has already voted
            if (hasUserVoted(username)) {
                JOptionPane.showMessageDialog(frame, "You have already voted.");
                return;
            }

            // Insert vote into the database
            String query = "INSERT INTO votes (username, candidate_name) VALUES (?, ?)";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, username);
                statement.setString(2, selectedCandidate);
                statement.executeUpdate();
                JOptionPane.showMessageDialog(frame, "Your vote for " + selectedCandidate + " has been cast.");
                showHomePage(); // After voting, show the home page
            }
        } catch (SQLException e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(frame, "Error while casting vote.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    // Handle user registration
    private static void handleRegister(String username, String password, String confirmPassword) {
        if (username.isEmpty() || password.isEmpty()) {
            JOptionPane.showMessageDialog(frame, "Username and password cannot be empty.", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        if (!password.equals(confirmPassword)) {
            JOptionPane.showMessageDialog(frame, "Passwords do not match.", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        if (isUsernameExists(username)) {
            JOptionPane.showMessageDialog(frame, "Username already exists.", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        // Insert user into the database
        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String query = "INSERT INTO users (username, password) VALUES (?, ?)";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, username);
                statement.setString(2, password); // Hash password here for better security
                statement.executeUpdate();
                JOptionPane.showMessageDialog(frame, "Registration successful!");
                showHomePage();  // Go back to the home page
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Check if the username already exists in the database
    private static boolean isUsernameExists(String username) {
        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String query = "SELECT * FROM users WHERE username = ?";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, username);
                ResultSet resultSet = statement.executeQuery();
                return resultSet.next();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }

    // Show officer login panel
 // Show officer login panel
    private static void showOfficerLoginPanel() {
        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(4, 2));  // Adjust grid layout to fit the back button

        JLabel usernameLabel = new JLabel("Officer Username:");
        JLabel passwordLabel = new JLabel("Password:");

        JTextField usernameField = new JTextField();
        JPasswordField passwordField = new JPasswordField();

        JButton loginButton = new JButton("Login");
        loginButton.addActionListener(e -> handleOfficerLogin(usernameField.getText(), new String(passwordField.getPassword())));

        // Back button to return to the home page
        JButton backButton = new JButton("Back");
        backButton.addActionListener(e -> showHomePage());

        panel.add(usernameLabel);
        panel.add(usernameField);
        panel.add(passwordLabel);
        panel.add(passwordField);
        panel.add(loginButton);
        panel.add(backButton);

        frame.getContentPane().removeAll();
        frame.getContentPane().add(panel);
        frame.revalidate();
        frame.repaint();
    }

    // Handle officer login
    private static void handleOfficerLogin(String username, String password) {
        if (isOfficerValid(username, password)) {
            JOptionPane.showMessageDialog(frame, "Officer logged in successfully!");
        } else {
            JOptionPane.showMessageDialog(frame, "Invalid officer username or password.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    // Validate officer login credentials from the database
    private static boolean isOfficerValid(String username, String password) {
        try (Connection connection = DriverManager.getConnection(DB_URL, USER, PASS)) {
            String query = "SELECT * FROM officers WHERE username = ? AND password = ?";
            try (PreparedStatement statement = connection.prepareStatement(query)) {
                statement.setString(1, username);
                statement.setString(2, password);
                ResultSet resultSet = statement.executeQuery();
                return resultSet.next();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }
}

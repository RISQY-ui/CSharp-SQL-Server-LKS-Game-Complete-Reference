# Complete Summary of C# and SQL Server Logic - LKS Game Application Ammunition for Faris

---

## 📋 Table of Contents

- [1. Introduction & Overview](#1-introduction--overview)
- [2. IF Logic Mechanism (Key Matching System)](#2-if-logic-mechanism-key-matching-system)
- [3. Database Bridge (SqlConnection)](#3-database-bridge-sqlconnection)
- [4. Input-Process-Output Flow](#4-input-process-output-flow)
- [5. Complete Code Examples](#5-complete-code-examples)
- [6. SQL Server Database Operations](#6-sql-server-database-operations)
- [7. Game Database Integration](#7-game-database-integration)
- [8. Best Practices & Tips](#8-best-practices--tips)
- [9. Quick Reference Guide](#9-quick-reference-guide)

---

## 1. Introduction & Overview

### Purpose of This Documentation

This document provides a comprehensive summary of **C# programming** and **SQL Server database** concepts that Faris has learned in preparation for the **LKS (Student Competency Competition)** in game application development.

### Topics Covered

- ✅ IF Logic Mechanism (data access gates)
- ✅ SqlConnection as a bridge to SQL Server database
- ✅ Input (TextBox) → Process (SELECT query) → Output (MessageBox) workflow
- ✅ Code examples with simple analogies
- ✅ Game database integration concepts
- ✅ Best practices for competitive programming

### Competition Context

| Aspect | Details |
|--------|---------|
| **Competition** | LKS (Lomba Kompetensi Siswa) |
| **Category** | Game Application Development |
| **Technologies** | C# (WinForms), SQL Server |
| **Goal** | Build functional game with database integration |
| **Preparation** | Master core logic before starting game development |

---

## 2. IF Logic Mechanism (Key Matching System)

### Core Concept

The `if (condition)` statement in C# works like a **security gate**. It checks whether a condition is true or false before executing code.

### Analogy: Door Lock and Key

```
User enters Password (Input)
        ↓
Check: Does password match database? (IF condition)
        ↓
Match ✅ → Open door (Execute code in if block)
No Match ❌ → Reject access (Execute code in else block)
```

### Basic Structure

```csharp
if (condition)
{
    // This code runs if condition is TRUE
}
else
{
    // This code runs if condition is FALSE
}
```

### Real Game Example: Login System

```csharp
// Get username input from TextBox
string inputUsername = textBoxUsername.Text;
string inputPassword = textBoxPassword.Text;

// Check if input matches what's in database
if (inputUsername == "admin" && inputPassword == "password123")
{
    // Condition TRUE - grant access
    MessageBox.Show("Login successful! Welcome, admin!");
    OpenGameWindow();
}
else
{
    // Condition FALSE - deny access
    MessageBox.Show("Invalid username or password!");
}
```

### IF with Database Query

```csharp
SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True");
connection.Open();

string query = "SELECT * FROM Users WHERE Username = @username AND Password = @password";
SqlCommand cmd = new SqlCommand(query, connection);
cmd.Parameters.AddWithValue("@username", inputUsername);
cmd.Parameters.AddWithValue("@password", inputPassword);

SqlDataReader reader = cmd.ExecuteReader();

// IF logic: Check if data was found
if (reader.HasRows)
{
    MessageBox.Show("Authentication successful!");
    reader.Read();
    string playerName = reader["PlayerName"].ToString();
    int playerLevel = (int)reader["Level"];
}
else
{
    MessageBox.Show("User not found in database!");
}

connection.Close();
```

### Complex IF Conditions

```csharp
// Multiple conditions with AND (&&)
if (playerHealth > 0 && playerMana > 0 && !isStunned)
{
    Console.WriteLine("Player can attack!");
}

// Multiple conditions with OR (||)
if (isDead || isDefeated || timeLimitReached)
{
    Console.WriteLine("Game Over!");
    EndGame();
}

// Nested IF statements
if (playerLevel >= 10)
{
    if (hasCompletedQuest)
    {
        if (inventory.Contains("MagicKey"))
        {
            Console.WriteLine("You can enter the secret chamber!");
        }
    }
}

// IF-ELSE IF chain
if (score >= 90)
{
    grade = "A";
}
else if (score >= 80)
{
    grade = "B";
}
else if (score >= 70)
{
    grade = "C";
}
else
{
    grade = "F";
}
```

### Comparison with Other Languages

The IF logic is **universal** across all programming languages:

| Language | Syntax |
|----------|--------|
| **C#** | `if (condition) { }` |
| **JavaScript** | `if (condition) { }` |
| **Python** | `if condition :` (no curly braces) |
| **Java** | `if (condition) { }` |
| **C++** | `if (condition) { }` |

The difference is only in **syntax**, the **logic is identical**.

---

## 3. Database Bridge (SqlConnection)

### What is SqlConnection?

`SqlConnection` is the **bridge** between your C# application and SQL Server database. Think of it as a telephone line connecting your game to the database server.

### Basic Syntax

```csharp
SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True");
```

### Connection String Components

| Component | Purpose | Example |
|-----------|---------|---------|
| **Data Source** | Location of SQL Server | `localhost` (your computer) or `server.name` |
| **Initial Catalog** | Database name | `GameDB`, `hotelnew`, `StudentDB` |
| **Integrated Security** | Authentication method | `True` (Windows login) or `False` (SQL login) |
| **User Id** | Username (if not Windows auth) | `sa` |
| **Password** | Password (if not Windows auth) | `YourPassword123` |

### Connection String Examples

```csharp
// Local development (Windows Authentication)
string localConnection = "Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True";

// With specific credentials
string credentialConnection = "Data Source=SERVER;Initial Catalog=GameDB;User Id=sa;Password=AdminPassword;";

// Remote server
string remoteConnection = "Data Source=192.168.1.100;Initial Catalog=GameDB;User Id=admin;Password=secure123;";

// Connection with timeout
string robustConnection = "Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True;Connection Timeout=30;";
```

### Opening and Closing Connection

```csharp
SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True");

try
{
    connection.Open();  // Establish connection
    Console.WriteLine("Connected to database successfully!");
    
    // Perform database operations here
    
    connection.Close();  // Close connection (important!)
}
catch (Exception ex)
{
    Console.WriteLine($"Connection error: {ex.Message}");
}
```

### Using 'using' Statement (Best Practice)

Automatically closes connection even if error occurs:

```csharp
string connectionString = "Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True";

using (SqlConnection connection = new SqlConnection(connectionString))
{
    connection.Open();
    
    // Database operations here
    
    // Connection automatically closes when exiting this block
}
```

### Connection Pool

After connecting, the connection is stored in a pool for reuse:

```csharp
// First connection - creates new connection to server
using (SqlConnection connection1 = new SqlConnection(connectionString))
{
    connection1.Open();
}

// Second connection - reuses from pool if within 15 minutes
using (SqlConnection connection2 = new SqlConnection(connectionString))
{
    connection2.Open();  // Faster than first connection
}
```

### Common Connection Errors

| Error | Cause | Solution |
|-------|-------|----------|
| "Cannot open server" | Server address wrong or server down | Check Data Source value |
| "Cannot open database" | Database name wrong | Check Initial Catalog spelling |
| "Login failed" | Wrong credentials | Verify User Id and Password |
| "Connection timeout" | Server not responding | Increase Connection Timeout or check network |

---

## 4. Input-Process-Output Flow

### The Three-Stage Model

Every application follows this fundamental pattern:

```
INPUT → PROCESS → OUTPUT
```

### Detailed Breakdown

#### Stage 1: INPUT (User provides data)

**Where data comes from:**
- TextBox (user types)
- ComboBox (user selects)
- Console (user enters)
- Database (retrieve existing data)

```csharp
// Getting input from TextBox
string playerName = textBoxName.Text;
int playerAge = int.Parse(textBoxAge.Text);
string selectedClass = comboBoxClass.SelectedItem.ToString();

// Getting input from Console
Console.Write("Enter your name: ");
string name = Console.ReadLine();

// Getting input from Database
SqlCommand cmd = new SqlCommand("SELECT * FROM Players WHERE Id = 1", connection);
SqlDataReader reader = cmd.ExecuteReader();
```

#### Stage 2: PROCESS (Application manipulates data)

**What happens during processing:**
- Query database (SELECT, INSERT, UPDATE, DELETE)
- Calculate values
- Validate input
- Apply business logic

```csharp
// Query the database
string query = "SELECT * FROM Users WHERE Username = @username";
SqlCommand cmd = new SqlCommand(query, connection);
cmd.Parameters.AddWithValue("@username", playerName);
SqlDataReader reader = cmd.ExecuteReader();

// Apply logic
if (reader.HasRows)
{
    // Data found - process it
    reader.Read();
    int level = (int)reader["Level"];
    int newLevel = level + 1;  // Calculate
}
else
{
    // Data not found - handle error
    isUserValid = false;
}
```

#### Stage 3: OUTPUT (Application presents result)

**Where results are shown:**
- MessageBox (popup dialog)
- Label (text on form)
- Console (text output)
- ListBox (display list)
- Database (save result)

```csharp
// Output to MessageBox
MessageBox.Show("Login successful!");

// Output to Label
labelStatus.Text = "Player Level: " + newLevel;

// Output to Console
Console.WriteLine("Game started successfully");

// Output to ListBox
listBoxPlayers.Items.Add(playerName);

// Output to Database (INSERT)
string insertQuery = "INSERT INTO GameLog (PlayerName, Level) VALUES (@name, @level)";
SqlCommand insertCmd = new SqlCommand(insertQuery, connection);
insertCmd.Parameters.AddWithValue("@name", playerName);
insertCmd.Parameters.AddWithValue("@level", newLevel);
insertCmd.ExecuteNonQuery();
```

### Complete Input-Process-Output Example

```csharp
private void buttonLogin_Click(object sender, EventArgs e)
{
    // ========== STAGE 1: INPUT ==========
    string inputUsername = textBoxUsername.Text;
    string inputPassword = textBoxPassword.Text;
    
    // Validate input
    if (string.IsNullOrEmpty(inputUsername) || string.IsNullOrEmpty(inputPassword))
    {
        MessageBox.Show("Please enter username and password!");
        return;
    }
    
    // ========== STAGE 2: PROCESS ==========
    using (SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True"))
    {
        try
        {
            connection.Open();
            
            // Query database
            string query = "SELECT * FROM Users WHERE Username = @username AND Password = @password";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@username", inputUsername);
            cmd.Parameters.AddWithValue("@password", inputPassword);
            
            SqlDataReader reader = cmd.ExecuteReader();
            
            if (reader.HasRows)
            {
                reader.Read();
                int userId = (int)reader["Id"];
                string playerName = reader["PlayerName"].ToString();
                int level = (int)reader["Level"];
                
                // ========== STAGE 3: OUTPUT ==========
                MessageBox.Show($"Welcome {playerName}! You are Level {level}");
                
                // Open main game window
                FormGame gameForm = new FormGame(userId);
                gameForm.Show();
                this.Hide();
            }
            else
            {
                // ========== STAGE 3: OUTPUT (Failure) ==========
                MessageBox.Show("Invalid username or password!");
            }
        }
        catch (Exception ex)
        {
            MessageBox.Show($"Error: {ex.Message}");
        }
    }
}
```

### Flow Diagram

```
┌─────────────────┐
│   INPUT Stage   │
│  Get user data  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ PROCESS Stage   │
│ Query database  │
│ Calculate logic │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  OUTPUT Stage   │
│ Show results    │
│ Save to database│
└─────────────────┘
```

---

## 5. Complete Code Examples

### Example 1: User Registration System

```csharp
private void RegisterNewPlayer()
{
    // INPUT: Get data from form
    string username = textBoxUsername.Text;
    string password = textBoxPassword.Text;
    string playerName = textBoxPlayerName.Text;
    string emailAddress = textBoxEmail.Text;
    
    // Validate input
    if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
    {
        MessageBox.Show("Username and password are required!");
        return;
    }
    
    if (password.Length < 6)
    {
        MessageBox.Show("Password must be at least 6 characters!");
        return;
    }
    
    // PROCESS: Insert into database
    using (SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True"))
    {
        try
        {
            connection.Open();
            
            // Check if username already exists
            string checkQuery = "SELECT COUNT(*) FROM Users WHERE Username = @username";
            SqlCommand checkCmd = new SqlCommand(checkQuery, connection);
            checkCmd.Parameters.AddWithValue("@username", username);
            int count = (int)checkCmd.ExecuteScalar();
            
            if (count > 0)
            {
                // OUTPUT: Username taken
                MessageBox.Show("Username already exists! Please choose another.");
                return;
            }
            
            // Insert new user
            string insertQuery = "INSERT INTO Users (Username, Password, PlayerName, Email, CreatedDate, Level) " +
                                "VALUES (@username, @password, @playerName, @email, @createdDate, 1)";
            SqlCommand insertCmd = new SqlCommand(insertQuery, connection);
            insertCmd.Parameters.AddWithValue("@username", username);
            insertCmd.Parameters.AddWithValue("@password", password);
            insertCmd.Parameters.AddWithValue("@playerName", playerName);
            insertCmd.Parameters.AddWithValue("@email", emailAddress);
            insertCmd.Parameters.AddWithValue("@createdDate", DateTime.Now);
            
            insertCmd.ExecuteNonQuery();
            
            // OUTPUT: Success
            MessageBox.Show("Account created successfully! You can now login.");
            ClearForm();
        }
        catch (Exception ex)
        {
            // OUTPUT: Error
            MessageBox.Show($"Registration failed: {ex.Message}");
        }
    }
}
```

### Example 2: Game Save System

```csharp
private void SaveGameProgress(int playerId, int currentLevel, int currentExp, int goldCollected)
{
    using (SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True"))
    {
        try
        {
            connection.Open();
            
            // PROCESS: Update player progress
            string updateQuery = @"UPDATE Players 
                                 SET Level = @level, 
                                     Experience = @exp, 
                                     Gold = @gold, 
                                     LastSavedTime = @savedTime 
                                 WHERE Id = @playerId";
            
            SqlCommand cmd = new SqlCommand(updateQuery, connection);
            cmd.Parameters.AddWithValue("@level", currentLevel);
            cmd.Parameters.AddWithValue("@exp", currentExp);
            cmd.Parameters.AddWithValue("@gold", goldCollected);
            cmd.Parameters.AddWithValue("@savedTime", DateTime.Now);
            cmd.Parameters.AddWithValue("@playerId", playerId);
            
            int rowsAffected = cmd.ExecuteNonQuery();
            
            if (rowsAffected > 0)
            {
                // OUTPUT: Success
                MessageBox.Show("Game saved successfully!");
            }
            else
            {
                // OUTPUT: No update
                MessageBox.Show("Failed to save game!");
            }
        }
        catch (Exception ex)
        {
            // OUTPUT: Error
            MessageBox.Show($"Save error: {ex.Message}");
        }
    }
}
```

### Example 3: Leaderboard Display

```csharp
private void LoadLeaderboard()
{
    using (SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True"))
    {
        try
        {
            connection.Open();
            
            // PROCESS: Query top players
            string query = "SELECT TOP 10 PlayerName, Level, Experience, Gold FROM Players ORDER BY Level DESC, Experience DESC";
            SqlCommand cmd = new SqlCommand(query, connection);
            
            SqlDataReader reader = cmd.ExecuteReader();
            
            listBoxLeaderboard.Items.Clear();
            int rank = 1;
            
            while (reader.Read())
            {
                string playerName = reader["PlayerName"].ToString();
                int level = (int)reader["Level"];
                int experience = (int)reader["Experience"];
                int gold = (int)reader["Gold"];
                
                // OUTPUT: Display in ListBox
                string leaderboardEntry = $"{rank}. {playerName} - Level {level} (Exp: {experience}, Gold: {gold})";
                listBoxLeaderboard.Items.Add(leaderboardEntry);
                rank++;
            }
        }
        catch (Exception ex)
        {
            // OUTPUT: Error
            MessageBox.Show($"Error loading leaderboard: {ex.Message}");
        }
    }
}
```

### Example 4: Quest Completion Tracker

```csharp
private void CompleteQuest(int playerId, int questId, int rewardExp, int rewardGold)
{
    using (SqlConnection connection = new SqlConnection("Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True"))
    {
        try
        {
            connection.Open();
            
            // PROCESS: Mark quest as completed
            string questQuery = "UPDATE Quests SET IsCompleted = 1, CompletedDate = @completedDate WHERE Id = @questId AND PlayerId = @playerId";
            SqlCommand questCmd = new SqlCommand(questQuery, connection);
            questCmd.Parameters.AddWithValue("@questId", questId);
            questCmd.Parameters.AddWithValue("@playerId", playerId);
            questCmd.Parameters.AddWithValue("@completedDate", DateTime.Now);
            
            questCmd.ExecuteNonQuery();
            
            // PROCESS: Add rewards to player
            string rewardQuery = "UPDATE Players SET Experience = Experience + @exp, Gold = Gold + @gold WHERE Id = @playerId";
            SqlCommand rewardCmd = new SqlCommand(rewardQuery, connection);
            rewardCmd.Parameters.AddWithValue("@exp", rewardExp);
            rewardCmd.Parameters.AddWithValue("@gold", rewardGold);
            rewardCmd.Parameters.AddWithValue("@playerId", playerId);
            
            rewardCmd.ExecuteNonQuery();
            
            // OUTPUT: Success
            MessageBox.Show($"Quest completed! You earned {rewardExp} EXP and {rewardGold} Gold!");
        }
        catch (Exception ex)
        {
            // OUTPUT: Error
            MessageBox.Show($"Error completing quest: {ex.Message}");
        }
    }
}
```

---

## 6. SQL Server Database Operations

### CREATE (Insert New Data)

```csharp
private void CreateNewPlayer(string name, string email)
{
    using (SqlConnection connection = new SqlConnection(connectionString))
    {
        connection.Open();
        
        string query = "INSERT INTO Players (Name, Email, CreatedDate, Level, Gold) " +
                      "VALUES (@name, @email, @createdDate, 1, 0)";
        
        SqlCommand cmd = new SqlCommand(query, connection);
        cmd.Parameters.AddWithValue("@name", name);
        cmd.Parameters.AddWithValue("@email", email);
        cmd.Parameters.AddWithValue("@createdDate", DateTime.Now);
        
        int rowsAffected = cmd.ExecuteNonQuery();
        
        if (rowsAffected > 0)
        {
            MessageBox.Show("Player created successfully!");
        }
    }
}
```

### READ (Retrieve Data)

```csharp
private void GetPlayerInfo(int playerId)
{
    using (SqlConnection connection = new SqlConnection(connectionString))
    {
        connection.Open();
        
        string query = "SELECT * FROM Players WHERE Id = @playerId";
        SqlCommand cmd = new SqlCommand(query, connection);
        cmd.Parameters.AddWithValue("@playerId", playerId);
        
        SqlDataReader reader = cmd.ExecuteReader();
        
        if (reader.Read())
        {
            string playerName = reader["Name"].ToString();
            int level = (int)reader["Level"];
            int gold = (int)reader["Gold"];
            
            labelPlayerInfo.Text = $"Name: {playerName}, Level: {level}, Gold: {gold}";
        }
        else
        {
            MessageBox.Show("Player not found!");
        }
    }
}
```

### UPDATE (Modify Existing Data)

```csharp
private void UpdatePlayerLevel(int playerId, int newLevel)
{
    using (SqlConnection connection = new SqlConnection(connectionString))
    {
        connection.Open();
        
        string query = "UPDATE Players SET Level = @level WHERE Id = @playerId";
        SqlCommand cmd = new SqlCommand(query, connection);
        cmd.Parameters.AddWithValue("@level", newLevel);
        cmd.Parameters.AddWithValue("@playerId", playerId);
        
        int rowsAffected = cmd.ExecuteNonQuery();
        
        if (rowsAffected > 0)
        {
            MessageBox.Show($"Player level updated to {newLevel}!");
        }
    }
}
```

### DELETE (Remove Data)

```csharp
private void DeletePlayer(int playerId)
{
    if (MessageBox.Show("Are you sure? This cannot be undone!", "Confirm Delete", MessageBoxButtons.YesNo) == DialogResult.Yes)
    {
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();
            
            string query = "DELETE FROM Players WHERE Id = @playerId";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@playerId", playerId);
            
            int rowsAffected = cmd.ExecuteNonQuery();
            
            if (rowsAffected > 0)
            {
                MessageBox.Show("Player deleted successfully!");
            }
        }
    }
}
```

---

## 7. Game Database Integration

### Database Schema for Game

```sql
CREATE TABLE Players (
    Id INT PRIMARY KEY IDENTITY,
    Username NVARCHAR(50) UNIQUE NOT NULL,
    Password NVARCHAR(50) NOT NULL,
    PlayerName NVARCHAR(100) NOT NULL,
    Level INT DEFAULT 1,
    Experience INT DEFAULT 0,
    Gold INT DEFAULT 0,
    CreatedDate DATETIME DEFAULT GETDATE()
);

CREATE TABLE Quests (
    Id INT PRIMARY KEY IDENTITY,
    PlayerId INT NOT NULL,
    QuestName NVARCHAR(100) NOT NULL,
    IsCompleted BIT DEFAULT 0,
    RewardExp INT DEFAULT 0,
    RewardGold INT DEFAULT 0,
    CompletedDate DATETIME NULL,
    FOREIGN KEY (PlayerId) REFERENCES Players(Id)
);

CREATE TABLE Inventory (
    Id INT PRIMARY KEY IDENTITY,
    PlayerId INT NOT NULL,
    ItemName NVARCHAR(100) NOT NULL,
    Quantity INT DEFAULT 1,
    AddedDate DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (PlayerId) REFERENCES Players(Id)
);

CREATE TABLE GameLog (
    Id INT PRIMARY KEY IDENTITY,
    PlayerId INT NOT NULL,
    Action NVARCHAR(200) NOT NULL,
    LogDate DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (PlayerId) REFERENCES Players(Id)
);
```

### Game Manager Class (Database Operations)

```csharp
public class GameManager
{
    private string connectionString = "Data Source=localhost;Initial Catalog=GameDB;Integrated Security=True";
    
    // Add item to player inventory
    public void AddItemToInventory(int playerId, string itemName, int quantity = 1)
    {
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();
            
            // Check if item already exists
            string checkQuery = "SELECT * FROM Inventory WHERE PlayerId = @playerId AND ItemName = @itemName";
            SqlCommand checkCmd = new SqlCommand(checkQuery, connection);
            checkCmd.Parameters.AddWithValue("@playerId", playerId);
            checkCmd.Parameters.AddWithValue("@itemName", itemName);
            
            SqlDataReader reader = checkCmd.ExecuteReader();
            
            if (reader.HasRows)
            {
                // Update quantity
                reader.Close();
                string updateQuery = "UPDATE Inventory SET Quantity = Quantity + @quantity WHERE PlayerId = @playerId AND ItemName = @itemName";
                SqlCommand updateCmd = new SqlCommand(updateQuery, connection);
                updateCmd.Parameters.AddWithValue("@quantity", quantity);
                updateCmd.Parameters.AddWithValue("@playerId", playerId);
                updateCmd.Parameters.AddWithValue("@itemName", itemName);
                updateCmd.ExecuteNonQuery();
            }
            else
            {
                // Insert new item
                reader.Close();
                string insertQuery = "INSERT INTO Inventory (PlayerId, ItemName, Quantity) VALUES (@playerId, @itemName, @quantity)";
                SqlCommand insertCmd = new SqlCommand(insertQuery, connection);
                insertCmd.Parameters.AddWithValue("@playerId", playerId);
                insertCmd.Parameters.AddWithValue("@itemName", itemName);
                insertCmd.Parameters.AddWithValue("@quantity", quantity);
                insertCmd.ExecuteNonQuery();
            }
        }
    }
    
    // Get player inventory
    public List<InventoryItem> GetPlayerInventory(int playerId)
    {
        List<InventoryItem> inventory = new List<InventoryItem>();
        
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();
            
            string query = "SELECT ItemName, Quantity FROM Inventory WHERE PlayerId = @playerId";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@playerId", playerId);
            
            SqlDataReader reader = cmd.ExecuteReader();
            
            while (reader.Read())
            {
                inventory.Add(new InventoryItem
                {
                    ItemName = reader["ItemName"].ToString(),
                    Quantity = (int)reader["Quantity"]
                });
            }
        }
        
        return inventory;
    }
    
    // Award experience to player
    public void AwardExperience(int playerId, int experienceAmount)
    {
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();
            
            string query = "UPDATE Players SET Experience = Experience + @exp WHERE Id = @playerId";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@exp", experienceAmount);
            cmd.Parameters.AddWithValue("@playerId", playerId);
            
            cmd.ExecuteNonQuery();
            
            // Log this action
            LogGameAction(playerId, $"Gained {experienceAmount} experience");
        }
    }
    
    // Log game actions
    private void LogGameAction(int playerId, string action)
    {
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            connection.Open();
            
            string query = "INSERT INTO GameLog (PlayerId, Action) VALUES (@playerId, @action)";
            SqlCommand cmd = new SqlCommand(query, connection);
            cmd.Parameters.AddWithValue("@playerId", playerId);
            cmd.Parameters.AddWithValue("@action", action);
            
            cmd.ExecuteNonQuery();
        }
    }
}

// Helper class for inventory
public class InventoryItem
{
    public string ItemName { get; set; }
    public int Quantity { get; set; }
}
```

---

## 8. Best Practices & Tips

### 1. Always Use Parameters for Queries

**❌ WRONG - Vulnerable to SQL Injection:**
```csharp
string query = "SELECT * FROM Users WHERE Username = '" + username + "'";
```

**✅ CORRECT - Safe with parameters:**
```csharp
string query = "SELECT * FROM Users WHERE Username = @username";
SqlCommand cmd = new SqlCommand(query, connection);
cmd.Parameters.AddWithValue("@username", username);
```

### 2. Always Close Connections

**❌ WRONG - Resource leak:**
```csharp
SqlConnection connection = new SqlConnection(connectionString);
connection.Open();
// Do something
// Forgot to close!
```

**✅ CORRECT - Using statement automatically closes:**
```csharp
using (SqlConnection connection = new SqlConnection(connectionString))
{
    connection.Open();
    // Do something
    // Automatically closes
}
```

### 3. Validate User Input

```csharp
// Check for empty input
if (string.IsNullOrEmpty(textBoxUsername.Text))
{
    MessageBox.Show("Username cannot be empty!");
    return;
}

// Check string length
if (textBoxPassword.Text.Length < 6)
{
    MessageBox.Show("Password must be at least 6 characters!");
    return;
}

// Check numeric input
if (!int.TryParse(textBoxAge.Text, out int age))
{
    MessageBox.Show("Please enter a valid number for age!");
    return;
}
```

### 4. Use Meaningful Variable Names

**❌ Bad:**
```csharp
string a = textBox1.Text;
int b = int.Parse(textBox2.Text);
```

**✅ Good:**
```csharp
string playerUsername = textBoxUsername.Text;
int playerLevel = int.Parse(textBoxLevel.Text);
```

### 5. Handle Exceptions Properly

```csharp
try
{
    // Database operation
}
catch (SqlException sqlEx)
{
    // SQL-specific error
    MessageBox.Show($"Database error: {sqlEx.Message}");
}
catch (FormatException fEx)
{
    // Format conversion error
    MessageBox.Show($"Input format error: {fEx.Message}");
}
catch (Exception ex)
{
    // Generic error
    MessageBox.Show($"Unexpected error: {ex.Message}");
}
finally
{
    // Clean up if needed
}
```

### 6. Comment Your Code

```csharp
// Retrieve player data from database
string query = "SELECT * FROM Players WHERE Id = @playerId";
SqlCommand cmd = new SqlCommand(query, connection);
cmd.Parameters.AddWithValue("@playerId", playerId);

// Execute query and check if data exists
SqlDataReader reader = cmd.ExecuteReader();
if (reader.HasRows)
{
    // Player found - extract data
    reader.Read();
    string playerName = reader["Name"].ToString();
}
else
{
    // Player not found - show error
    MessageBox.Show("Player not found!");
}
```

### 7. Test Edge Cases

```csharp
// Test with various inputs
List<string> testCases = new List<string>
{
    "",                     // Empty
    null,                   // Null
    "   ",                  // Only spaces
    "123",                  // Numbers
    "@#$%",                 // Special characters
    "Normal Name",          // Normal input
    "VeryLongNameWithManyCharactersToTestLimits" // Very long
};

foreach (string testInput in testCases)
{
    // Test your code
}
```

---

## 9. Quick Reference Guide

### Common SQL Queries for Games

```csharp
// Get all players sorted by level
"SELECT * FROM Players ORDER BY Level DESC"

// Get top 10 players
"SELECT TOP 10 * FROM Players ORDER BY Level DESC, Experience DESC"

// Count players
"SELECT COUNT(*) FROM Players"

// Get player by username
"SELECT * FROM Players WHERE Username = @username"

// Update multiple fields
"UPDATE Players SET Level = @level, Gold = @gold WHERE Id = @playerId"

// Delete completed quests
"DELETE FROM Quests WHERE IsCompleted = 1 AND DATEDIFF(DAY, CompletedDate, GETDATE()) > 30"

// Get inventory count
"SELECT COUNT(*) FROM Inventory WHERE PlayerId = @playerId"

// Get total gold
"SELECT SUM(Gold) FROM Players"
```

### Common C# Database Patterns

| Pattern | Code |
|---------|------|
| **Single value** | `var result = cmd.ExecuteScalar();` |
| **Multiple rows** | `SqlDataReader reader = cmd.ExecuteReader();` |
| **Insert/Update/Delete** | `int rowsAffected = cmd.ExecuteNonQuery();` |
| **Check existence** | `if (reader.HasRows) { }` |
| **Loop through results** | `while (reader.Read()) { }` |

### Troubleshooting Checklist

| Issue | Solution |
|-------|----------|
| "Connection timeout" | Check server is running |
| "Cannot open database" | Verify database name |
| "Login failed" | Check credentials |
| "Null reference" | Initialize objects before use |
| "Column not found" | Check column name spelling |
| "Invalid cast" | Verify data type matches |
| "Duplicate key" | Check for PRIMARY KEY violations |
| "Foreign key violation" | Ensure referenced record exists |

---

## 📚 Summary

### Key Concepts

1. **IF Logic** - Gate mechanism for controlling program flow
2. **SqlConnection** - Bridge between application and database
3. **Input-Process-Output** - Three-stage workflow for data handling
4. **CRUD Operations** - Create, Read, Update, Delete database records
5. **Parameters** - Safe method for passing values to SQL queries
6. **Exception Handling** - Gracefully manage errors

### Essential Files for Competition

Create and organize these files:

```
📁 LKS Game Project/
   ├── 📄 GameManager.cs (Database operations)
   ├── 📄 FormLogin.cs (User authentication)
   ├── 📄 FormGame.cs (Main game window)
   ├── 📄 FormLeaderboard.cs (Scoreboard)
   ├── 📁 Database/
   │   ├── 📄 DatabaseSchema.sql (Create tables)
   │   └── 📄 InitialData.sql (Sample data)
   └── 📄 Config.txt (Connection string)
```

### Competition Tips

✅ Start with database design first
✅ Test all CRUD operations before game logic
✅ Use meaningful names for variables and methods
✅ Always validate user input
✅ Handle exceptions gracefully
✅ Comment important code sections
✅ Keep game mechanics separate from database code
✅ Test with various inputs and edge cases

---

**Last Updated:** June 2026

**Created by:** Faris

**Purpose:** LKS Competition Preparation - Complete C# and SQL Server Reference

**Status:** ✅ Complete & Ready for Implementation

---

> **Remember:** Master the fundamentals first. A solid foundation in database connectivity and logic will make your game development much smoother and faster. Good luck with LKS! 🚀

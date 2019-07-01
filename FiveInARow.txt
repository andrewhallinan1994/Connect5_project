import java.net.*;
import java.io.*;
import java.util.Scanner;

public class FiveInARow
{
    private static char board[][];
    private static final int BOARD_WIDTH = 9;  //7 originally
    private static final int BOARD_HEIGHT = 6;
    private static boolean gameEnd = false;

    public static void main(String[] args)
    {
        board = new char[BOARD_HEIGHT + 1][BOARD_WIDTH + 1];  //Position 0 in char array 'board' isn't used, so numbers entered by player match up with the correct column
        Scanner input = new Scanner(System.in);     //Scanner reads input from stdin (standard input)
        char turn = 'x';                        //turn can be equal to 'x' or 'o'
        int dropColumn = 0;
        String playerNameX = enterNamesX();
        String playerNameO = enterNamesO();

        System.out.println("TWO PLAYER FIVE IN A ROW GAME! Initialising board now...");
        InitializeBoard();

        while (gameEnd == false)            //During gameplay, i.i while the game isn't over yet
        {
            displayBoard();                 //displays the board in the console to the player
            dropColumn = GetDropColumn(turn, input, playerNameX, playerNameO) - 1;  //uses GetDropColumn method to get dropColumn int value from player

            if (DropChecker(turn, dropColumn) == true) {                                //if the checker has been dropped...
                if (checkForFiveInARow(turn)) {  //checks if player has won
                    displayBoard();
                    System.out.println("Winner="+turn);
                    gameEnd = true;
                }
                turn = 'x' == turn ? 'o' : 'x';                                         //...it's now the other player's turn...
            }
            else {
                System.out.println("That column is full. Please enter again.");     //...otherwise the current player has to enter their input again.
            }
        }
    }

    public static String enterNamesX() {
        System.out.println("Please enter the name of playerX:");

        Scanner scannerX = new Scanner(System.in);
        String playerNameX = scannerX.next();
        //scannerX.close();

        System.out.println("Player X name is: " + playerNameX);
        return playerNameX;
    }

    public static String enterNamesO() {
        System.out.println("Please enter the name of playerO:");

        Scanner scannerO = new Scanner(System.in);
        String playerNameO = scannerO.next();
        //scannerO.close();

        System.out.println("Player O name is: " + playerNameO);
        return playerNameO;
    }


    // Sets all the elements of the two-dimensional 'board' array to empty spaces
    private static void InitializeBoard()
    {
        char blank = ' ';
        for (int i = 0; i < board.length; i++)
        {
            for (int e = 0; e < board[i].length; e++)
                board[i][e] = blank;            //sets every index position in the board array to blank
        }
    }


    private static void displayBoard()                     // Displays the game board
    {
        for (int row = 0; row < board.length; row++)             //increments to the next row
        {
            for (int col = 0; col < board.length +2; col++)       //increments through each column in the current row
            {
                System.out.print("|");                              //prints table border
                System.out.printf("%2c", board[row][col]);          //prints current element
            }
            System.out.println();
        }
    }

    //Gets drop column from the current player
    private static int GetDropColumn(char turn, Scanner input, String playerNameX, String playerNameO)
    {
        int numInput = 0;
        int realInput = 0;
        while (realInput == 0)
        {
            try
            {
                if(turn == 'x') {
                    System.out.println("It's your turn " + playerNameX + " for " + turn + ", please enter column (1-9)");
                }

                else if(turn == 'o'){
                    System.out.println("It's your turn " + playerNameO + " for " + turn + ", please enter column (1-9)");
                }

                numInput = input.nextInt();
                if (numInput < 1 || numInput > BOARD_WIDTH)
                {
                    numInput = 0;
                    System.out.println("The number was out of bounds. Please try again.");
                }
            }
            catch (NumberFormatException e)
            {
                System.out.println("Invalid input. Please try again.");         //If user inputs a non integer it returns an error, asks them ti enter again
            }
            realInput = numInput;
        }
        return realInput;
    }


    private static boolean DropChecker(char turn, int dropColumn)       //this method returns true if a checker is successfully dropped into the board
    {
        int indexToPaceChecker = BOARD_HEIGHT;      //indexToPaceChecker is equal to the height of the board (6)
        while (indexToPaceChecker >= 0)
        {
            if (board[indexToPaceChecker][dropColumn] == ' ')
            {
                //drops the checker into the board
                board[indexToPaceChecker][dropColumn] = turn;
                return true;
            }
            indexToPaceChecker--;                   //indexToPaceChecker is decremented by 1, e.g from 6 down to 5...
        }                                              //... when it goes below 0 that column is now full...
        return false;                                   //...then the boolean DropChecker is returned as false
    }










    // Checks for a line of five in a row in any direction
    private static boolean checkForFiveInARow(char turn) {
        // Diagonal line left to right
        for (int row = board.length-1; row >= 3; row--) {       //(int row = 7-1; row >= 3; row--)
            for (int col = 0; col <= board.length-4; col++) {    //(int col = 0; col <= 7-4; col++)
                boolean isLine = true;
                for (int i = 0; i<5 && isLine; i++) {           // (int i = 0; i<4 && isLine; i++)
                    if (board[row-i][col+i] != turn)
                        isLine = false;
                }
                if (isLine)
                    return true;
            }
        }

        // Diagonal line right to left
        for (int row = board.length-1; row >= 3; row--) {          //(int row = 7-1; row >= 3; row--)
            for (int col = board.length -1; col >= 3; col--) {      //(int col = 6; col >=0; col--)    mine is: (int col = board.length -1; col >= 3; col--)
                boolean isLine = true;                          //boolean isline defines whether its a line of four in a row or not...
                for (int i = 0; i<5 && isLine; i++) {              //...if isLine = true, then its four in a row, and the player wins.
                    if (board[row-i][col-i] != turn)                //remember char turn = 'x' or 'o' on the board
                        isLine = false;
                }
                if (isLine)
                    return true;
            }
        }


        // Vertical line
        for (int row = board.length-1; row >= 3; row--) {       //(int row = 7-1; row >= 3; row--)
            for (int col = 0; col <= board.length-1; col++) {    //(int col = 0; col <= 7-1; col++)
                boolean isLine = true;
                for (int i = 0; i<5 && isLine; i++) {
                    if (board[row-i][col] != turn)
                        isLine = false;
                }
                if (isLine)
                    return true;
            }
        }


        // Horizontal line
        for (int row = board.length-1; row >= 3; row--) {       //(int row = 7-1; row >= 3; row--)
            for (int col = 0; col <= board.length-1; col++) {    //(int col = 0; col <= 7-1; col++)
                boolean isLine = true;
                for (int i = 0; i<5 && isLine; i++) {
                    if (board[row][col++] != turn)
                        isLine = false;
                }
                if (isLine)
                    return true;
            }
        }

        return false;
    }
}
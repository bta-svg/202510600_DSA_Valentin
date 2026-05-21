#include <iostream>
#include <string>

using namespace std;

// Global Constant Configuration
const int MAX_TRANSACTIONS = 100;
const int DEFAULT_PIN = 12345;
const int ALLOWED_ATTEMPTS = 3;

// Function Declarations (Prototypes)
bool authenticateUser();
void displayMainMenu();
void handleCheckBalance(int balance);
void handleDeposit(int &balance, string transactionHistory[], int &transactionCount);
void handleWithdrawal(int &balance, string transactionHistory[], int &transactionCount);
void handleTransactionHistory(const string transactionHistory[], int transactionCount);

int main() {
    // Phase 1: Authentication Guard
    if (!authenticateUser()) {
        cout << "\n\033[31mATM BLOCKED. Maximum login attempts exceeded.\033[0m" << endl;
        return 0; // Terminate system execution safely
    }

    // Phase 2: Session Initialization
    int currentBalance = 0;
    string transactionHistory[MAX_TRANSACTIONS];
    int transactionCount = 0;
    int selectedOption = 0;

    // Phase 3: Interactive Application Loop
    do {
        displayMainMenu();
        cout << "\nEnter your choice: ";
        if (!(cin >> selectedOption)) {
            cout << "\033[31m\nInvalid input type. Please enter a number.\033[0m" << endl;
            cin.clear();
            cin.ignore(10000, '\n');
            continue;
        }

        switch (selectedOption) {
            case 1:
                handleCheckBalance(currentBalance);
                break;
            case 2:
                handleDeposit(currentBalance, transactionHistory, transactionCount);
                break;
            case 3:
                handleWithdrawal(currentBalance, transactionHistory, transactionCount);
                break;
            case 4:
                handleTransactionHistory(transactionHistory, transactionCount);
                break;
            case 5:
                cout << "\n\033[33mThank You For Using BK ATM. Goodbye!\033[0m" << endl;
                break;
            default:
                cout << "\n\033[31mInvalid Choice. Please select a valid option (1-5).\033[0m" << endl;
        }
    } while (selectedOption != 5);

    return 0;
}

// Verification mechanism tracking user access attempts
bool authenticateUser() {
    int enteredPin;
    int remainingAttempts = ALLOWED_ATTEMPTS;

    cout << "\033[36m\t\t===============================" << endl;
    cout << "\t\t         BK ATM SYSTEM         " << endl;
    cout << "\t\t===============================\033[0m" << endl;

    do {
        cout << "\n\tEnter your PIN: ";
        cin >> enteredPin;
        remainingAttempts--;

        if (enteredPin == DEFAULT_PIN) {
            cout << "\n\033[32m\tLogin Successful!" << endl;
            cout << "\tWelcome to BK ATM\033[0m" << endl;
            return true;
        } else {
            cout << "\n\033[31m\tWrong PIN\033[0m" << endl;
            if (remainingAttempts > 0) {
                cout << "\033[33m\tRemaining Attempts: " << remainingAttempts << "\033[0m" << endl;
            }
        }
    } while (remainingAttempts > 0);

    return false;
}

// Formatted system landing menu
void displayMainMenu() {
    cout << "\n\033[34m=========== MAIN MENU ===========\033[0m" << endl;
    cout << " [1] Check Balance" << endl;
    cout << " [2] Deposit Money" << endl;
    cout << " [3] Withdraw Money" << endl;
    cout << " [4] Transaction History" << endl;
    cout << " [5] Exit" << endl;
    cout << "\033[34m=================================\033[0m" << endl;
}

// Output active accounting balances
void handleCheckBalance(int balance) {
    cout << "\n\033[32mCurrent Balance: " << balance << " FRW\033[0m" << endl;
}

// Process financial deposit increments and record state
void handleDeposit(int &balance, string transactionHistory[], int &transactionCount) {
    int depositAmount;
    cout << "\nEnter amount to deposit: ";
    cin >> depositAmount;

    if (depositAmount <= 0) {
        cout << "\033[31m\nInvalid deposit quantity.\033[0m" << endl;
        return;
    }

    balance += depositAmount;
    cout << "\n\033[32mDeposit Successful" << endl;
    cout << "New Balance: " << balance << " FRW\033[0m" << endl;

    if (transactionCount < MAX_TRANSACTIONS) {
        transactionHistory[transactionCount] = "Deposited: " + to_string(depositAmount) + " FRW";
        transactionCount++;
    }
}

// Process financial withdrawal adjustments after validating liquidity conditions
void handleWithdrawal(int &balance, string transactionHistory[], int &transactionCount) {
    int withdrawalAmount;
    cout << "\nEnter amount to withdraw: ";
    cin >> withdrawalAmount;

    if (withdrawalAmount <= 0) {
        cout << "\033[31m\nInvalid withdrawal quantity.\033[0m" << endl;
        return;
    }

    if (withdrawalAmount > balance) {
        cout << "\n\033[31mInsufficient Funds\033[0m" << endl;
    } else {
        balance -= withdrawalAmount;
        cout << "\n\033[32mWithdrawal Successful" << endl;
        cout << "Withdrawn Amount: " << withdrawalAmount << " FRW" << endl;
        cout << "Remaining Balance: " << balance << " FRW\033[0m" << endl;

        if (transactionCount < MAX_TRANSACTIONS) {
            transactionHistory[transactionCount] = "Withdrawn : " + to_string(withdrawalAmount) + " FRW";
            transactionCount++;
        }
    }
}

// Trace log arrays via iterating processes
void handleTransactionHistory(const string transactionHistory[], int transactionCount) {
    cout << "\n\033[36m====== TRANSACTION HISTORY ======\033[0m" << endl;
    if (transactionCount == 0) {
        cout << "\033[31mNo Transactions Found\033[0m" << endl;
    } else {
        for (int i = 0; i < transactionCount; i++) {
            cout << "  [" << (i + 1) << "] " << transactionHistory[i] << endl;
        }
    }
}

# test_demo

using namespace std;

class User {
public:
    string email;
    string password;
    string phone_number;

    User() = default;  // منشئ افتراضي
    User(string email, string password, string phone_number = "")
            : email(email), password(password), phone_number(phone_number) {}
};

class AuthenticationSystem {
private:
    unordered_map<string, User> users;

    bool isValidPhoneNumber(const string &phone_number) {
        return phone_number.size() == 11 && all_of(phone_number.begin(), phone_number.end(), ::isdigit);
    }

    bool verifyHuman() {
        srand(time(0));  // تهيئة مولد الأرقام العشوائية
        int num1 = rand() % 10 + 1; // رقم عشوائي بين 1 و10
        int num2 = rand() % 10 + 1;
        int operation = rand() % 3; // اختيار العملية الحسابية عشوائياً (0: جمع، 1: طرح، 2: ضرب)
        string question;
        int answer;
        if (operation == 0) {
            question = "What is " + to_string(num1) + " + " + to_string(num2) + "?";
            answer = num1 + num2;
        } else if (operation == 1) {
            question = "What is " + to_string(num1) + " - " + to_string(num2) + "?";
            answer = num1 - num2;
        } else {
            question = "What is " + to_string(num1) + " * " + to_string(num2) + "?";
            answer = num1 * num2;
        }
        cout << "Human verification: " << question << " ";
        int user_answer;
        cin >> user_answer;
        return user_answer == answer;
    }

    void sendSMS(const string &phone_number, int otp) {
        cout << "Sending SMS to " << phone_number << " with OTP: " << otp << endl;
    }

public:
    bool createAccount() {
        string email, password;
        cout << "Enter your email: ";
        cin >> email;
        cout << "Enter your password: ";
        cin >> password;

        if (users.find(email) != users.end()) {
            cout << "Email already exists." << endl;
            return false;
        }

        string phone_number;
        cout << "Enter your phone number (11 digits): ";
        cin >> phone_number;
        while (!isValidPhoneNumber(phone_number)) {
            cout << "Invalid phone number. Phone number must be 11 digits and all numbers." << endl;
            cout << "Enter your phone number (11 digits): ";
            cin >> phone_number;
        }

        if (!verifyHuman()) {
            cout << "Failed human verification." << endl;
            return false;
        }

        users[email] = User(email, password, phone_number);
        cout << "Account created for " << email << "." << endl;
        return true;
    }

    bool login() {
        string email, password;
        cout << "Enter your email: ";
        cin >> email;
        cout << "Enter your password: ";
        cin >> password;

        if (users.find(email) != users.end() && users[email].password == password) {
            if (!verifyHuman()) {
                cout << "Failed human verification." << endl;
                return false;
            }
            cout << "Welcome, " << email << "! You are now logged in." << endl;
            return true;
        }
        cout << "Invalid email or password." << endl;
        return false;
    }

    bool verifyPhone(const string &email) {
        if (users.find(email) == users.end()) {
            cout << "User not found." << endl;
            return false;
        }
        int otp = rand() % 9000 + 1000; // Generate a 4-digit OTP
        cout << "OTP sent to " << users[email].phone_number << ": " << otp << endl;
        sendSMS(users[email].phone_number, otp);
        int user_otp;
        cout << "Enter the OTP code: ";
        cin >> user_otp;
        if (user_otp == otp) {
            cout << "Phone verification successful." << endl;
            return true;
        } else {
            cout << "Invalid OTP." << endl;
            return false;
        }
    }
};

class Product {
public:
    string name;
    double weight;
    double price;

    Product(string name, double weight, double price)
            : name(name), weight(weight), price(price) {}
};

class Shipping {
private:
    vector<Product> products;
    string status;
    double total_weight;
    double total_price;

public:
    Shipping() : status("Not Shipped"), total_weight(0), total_price(0) {}

    void addProduct(const Product &product) {
        products.push_back(product);
        total_weight += product.weight;
        total_price += product.price;
        cout << "Added " << product.name << " weighing " << product.weight
             << " kg and priced at " << product.price << " EGP." << endl;
    }

    double calculateShippingCost() const {
        const double cost_per_kg = 5.0;
        double total_shipping_cost = total_weight * cost_per_kg;
        cout << "Total shipping cost: " << total_shipping_cost << " EGP." << endl;
        return total_shipping_cost;
    }

    double calculateTotalCost() const {
        double total_shipping_cost = calculateShippingCost();
        double total_cost = total_price + total_shipping_cost;
        cout << "Total cost (products + shipping): " << total_cost << " EGP." << endl;
        return total_cost;
    }

    void shipOrder() {
        if (products.empty()) {
            cout << "No products to ship." << endl;
            return;
        }
        status = "Shipped";
        cout << "Order has been shipped." << endl;
    }

    void getOrderStatus() const {
        cout << "Current status: " << status << endl;
    }
};

class Payment {
public:
    bool processPayment(double amount, string method) {
        while (method != "Visa" && method != "MasterCard" && method != "PayPal" && method != "Fawry") {
            cout << "Invalid payment method." << endl;
            cout << "Choose payment method (Visa, MasterCard, PayPal, Fawry): ";
            cin >> method;
        }

        if (method == "Visa" || method == "MasterCard") {
            string card_number, expiry_date, cvv;

            // Input and validate card number
            while (true) {
                cout << "Enter your 16-digit card number: ";
                cin >> card_number;

                // Validate length and check that it contains only digits
                if (card_number.length() == 16 && all_of(card_number.begin(), card_number.end(), ::isdigit)) {
                    break;  // Exit the loop if input is valid
                } else {
                    cout << "Invalid card number. It must be exactly 16 digits." << endl;
                }
            }

            // Input and validate expiry date
            cout << "Enter expiry date (MM/YY): ";
            cin >> expiry_date;
            while (expiry_date.length() != 5 || expiry_date[2] != '/' ||
                   !isdigit(expiry_date[0]) || !isdigit(expiry_date[1]) ||
                   !isdigit(expiry_date[3]) || !isdigit(expiry_date[4])) {
                cout << "Invalid expiry date format. Please enter as MM/YY." << endl;
                cout << "Enter expiry date (MM/YY): ";
                cin >> expiry_date;
            }

            // Input and validate CVV
            cout << "Enter CVV (3 digits): ";
            cin >> cvv;
            while (cvv.length() != 3 || !all_of(cvv.begin(), cvv.end(), ::isdigit)) {
                cout << "Invalid CVV. It must be exactly 3 digits." << endl;
                cout << "Enter CVV (3 digits): ";
                cin >> cvv;
            }
        }

        cout << "Processing " << method << " payment of " << amount << " EGP." << endl;
        cout << "Payment processed successfully." << endl;

        // Send confirmation message after payment
        sendConfirmationMessage(amount);
        return true;
    }

    void sendConfirmationMessage(double amount) {
        int reference_number = rand() % 900000 + 100000; // Generate a 6-digit reference number
        time_t now = time(0);
        tm *ltm = localtime(&now);
        char buffer[80];
        strftime(buffer, 80, "%Y-%m-%d %H:%M:%S", ltm);
        cout << "\nPayment Confirmation:\n"
             << "- Reference Number: " << reference_number << "\n"
             << "- Amount: " << amount << " EGP\n"
             << "- Date and Time: " << buffer << "\n"
             << "- Estimated Delivery Date: " << ltm->tm_mday + 5 << "-" << 1 + ltm->tm_mon
             << "-" << 1900 + ltm->tm_year << "\n" << endl;
    }
};

// مثال لاستخدام التطبيق
int main() {
    srand(time(0));
    AuthenticationSystem auth_system;
    int choice;

    do {
        cout << "1. Signup\n";
        cout << "2. Login\n";
        cout << "3. Exit\n";
        cout << "Choose an option: ";
        cin >> choice;

        switch (choice) {
            case 1:
                auth_system.createAccount();
                break;
            case 2:
                if (auth_system.login()) {
                    // تحقق من رقم الهاتف
                    string email;
                    cout << "Enter your email to verify phone: ";
                    cin >> email;
                    if (auth_system.verifyPhone(email)) {
                        Product product1("Laptop", 2, 10000);  // السعر بالجنيه المصري
                        Product product2("Phone", 0.5, 5000);  // السعر بالجنيه المصري
                        Shipping shipping;
                        shipping.addProduct(product1);
                        shipping.addProduct(product2);
                        double total_cost = shipping.calculateTotalCost();

                        // الدفع
                        Payment payment;
                        string payment_method;
                        cout << "Choose payment method (Visa, MasterCard, PayPal, Fawry): ";
                        cin >> payment_method;
                        if (payment.processPayment(total_cost, payment_method)) {
                            shipping.shipOrder();
                            shipping.getOrderStatus();
                        }
                    }
                }
                break;
            case 3:
                cout << "Exiting the program." << endl;
                break;
            default:
                cout << "Invalid choice, please try again." << endl;
        }
    } while (choice != 3);

    return 0;
}

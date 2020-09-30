# Source Code
```c++
#include "CItem.h"
#include "CUser.h"
 
//To check the size of each list
int CheckSizeItem();
int CheckSizeUser();
//Reading the files into the program
void ReadItemFile(CItem list[], int size);
void ReadUserFile(CUser list[], int size);
//Writing the files to their respective list
void WriteItemFile(CItem list[], int size);
void WriteUserFile(CUser list[], int size);
//login function
int Login(CUser userlist[], int usersize, CItem itemlist[], int itemsize);
//Callback Functions
int DoInitializePriceList(CItem list[], int size);
void DoDisplayFullPriceList(CItem list[], int size);
int DoAddItemToList(CItem list[], int size);
void DoSetItemPrice(CItem list[], int size);
void DoSetItemDiscountRate(CItem list[], int size);
void DoDisplayItem(CItem list[], int size);
void DoOrderCost(CItem list[], int size);
void DoTotalInvoice(CItem list[], int size);
void DoRemoveItemFromList(CItem list[], int size);
void DoQuit(CItem list[], int size);
void DoAddUser(CUser list[], int userlistSize);
void DoEditUser(CUser userlist[], int userlistSize);
int ManagerMenu(CItem list[], int itemlistSize, CUser userlist[], int userlistSize);
int AssistantMenu(CItem list[], int actualSize, CUser userlist[], int userlistSize);
 
int main() {
    int itemlistSize = CheckSizeItem();
    int userlistSize = CheckSizeUser();
    //Setting the max size of the list
    const int defaultSize = 25;
    //Assigning the file size to the list
    CItem list[defaultSize];
    CUser userlist[defaultSize];
 
    ReadUserFile(userlist, userlistSize);
    ReadItemFile(list, itemlistSize);
 
    Login(userlist, userlistSize, list, itemlistSize);
    
}
 
int CheckSizeItem()
{
    //Running ReadFile function, but counting each row instead of reading the file
    int j = 0;
    int data;
    string data2;
    double data3, data4;
 
    ifstream infile;
    infile.open("ItemList.txt");
    while (infile >> data >> data2 >> data3 >> data4) {
        j++;
    }
    infile.close();
    return j;
}
 
int CheckSizeUser()
{
    int size = 0;
    int password;
    string username;
    char usertype;
 
    ifstream infile;
    infile.open("UserList.txt");
    while (infile >> username >> password >> usertype) {
        size++;
    }
    infile.close();
    return size;
}
 
void ReadItemFile(CItem list[], int size)
{
    //Taking each column into their respective datatypes, then adding them to the object array
    int j = 0;
    string description;
    int data;
    string data2;
    double data3;
    double data4;
    ifstream infile;
    infile.open("ItemList.txt");
    while (j < size) {
        infile >> data >> data2 >> data3 >> data4;
        CItem itemlist(data, data2, data3, data4);
        list[j] = itemlist;
        j++;
    }
    infile.close();
}
 
void ReadUserFile(CUser list[], int size)
{
    int i = 0;
    int password;
    string username;
    char usertype;
    ifstream infile;
    infile.open("UserList.txt");
    while (i < size) {
        infile >> username >> password >> usertype;
        CUser tempuserlist(username, password, usertype);
        list[i] = tempuserlist;
        i++;
    }
    infile.close();
}
 
void WriteItemFile(CItem list[], int size)
{   
    //Doing the reverse of the ReadFile function, then outputting exactly the way it was read
    int j = 0;
    ofstream outfile;
    outfile.open("ItemList.txt");
    for (int i = 0; i < size; i++) {
 
        int code = list[i].GetCode();
        string description = list[i].GetDescription();
        double price = list[i].GetPrice();
        double discount = list[i].GetDiscountRate();
        outfile << code << "\t" << description << "\t";
        outfile << price << "\t" << discount << "\t" << endl;
    }
 
    outfile.close();
}
 
void WriteUserFile(CUser list[], int size)
{
    ofstream outfile;
    outfile.open("UserList.txt");
    for (int i = 0; i < size; i++) {
 
        string username = list[i].GetUsername();
        int password = list[i].GetPassword();
        char usertype = list[i].GetUserType();
        outfile << username << "\t" << password << "\t";
        outfile << usertype << "\t" << endl;
    }
 
    outfile.close();
}
 
int Login(CUser userlist[], int usersize, CItem itemlist[], int itemsize)
{
    
    string username = ""; 
    int password = 0;
    cout << "Username: "; 
    cin >> username;
    cout << endl << "Password: "; 
    cin >> password;
 
    for (int i = 0; i < usersize; i++) {
        //If the credentials match with the data, login will proceed to display either menu, depending on the userType
        if (username == userlist[i].GetUsername() && password == userlist[i].GetPassword()) {
            system("color 0A");
            cout << "Login successful!" << endl;
            Sleep(800);
            system("cls");
            system("color 07");
            if (userlist[i].GetUserType() == 'M') {
                ManagerMenu(itemlist, itemsize, userlist, usersize);
                return 0;
            }
            else{
                AssistantMenu(itemlist, itemsize, userlist, usersize);
            return 0;
            }
            
        }
        
 
    }
    system("color 0c");
    cout << "There was no match, try again.\n";
    Sleep(800);
    system("cls");
    system("color 07");
    //Reloads the login function if there wasn't a match
    Login(userlist, usersize, itemlist, itemsize);
    return 0;
}
 
int DoInitializePriceList(CItem list[], int size)
{
    char answer = 0;
    int newSize = 0, code;
    string description;
    double price, discount;
    for (int i = 0; i < size; i++) {
        //Scans list array for product code, if they equal zero, there isn't any data in the list
        if (list[i].GetCode() != 0) {
            cout << "File already exists. Overwrite? (y/n): ";
            cin >> answer;
            break;
        }
 
    }
 
    if (answer == 'y' || answer == 'Y') {
        cout << "Enter number of items to add: ";
        cin >> newSize;
 
 
        for (int i = 0; i < newSize; i++) {
            cout << "Enter item code & description: ";
            cin >> code >> description;
            cout << "Enter item price & discount rate: ";
            cin >> price >> discount;
 
            CItem templist(code, description, price, discount);
            list[i] = templist;
        }
        return newSize;
    }
    else {
        cout << "Continuing using file\n";
        return size;
    }
}
 
void DoDisplayFullPriceList(CItem list[], int size)
{
    cout << "------------------------------------------------\n";
    for (int i = 0; i < size; i++)
    {
        list[i].Display();
    }
    cout << "------------------------------------------------\n";
}
 
int DoAddItemToList(CItem list[], int size)
{
    int code = 0; string description = ""; double price = 0, discountRate = 0;
    cout << "Enter item code and description: ";
    cin >> code >> description;
    //Checking if code already exists
    for (int i = 0; i < size; i++) {
        //Trying to add an item to the list with the same code/description won't work
        if (list[i].HasCode(code)) {
            cout << "Item code already exists, try again\n";
            cin.clear();
            DoAddItemToList(list, size);
            return 0;
        }
 
        if (description == list[i].GetDescription()) {
            cout << "An item with the same description has been found, try again\n";
            cin.clear();
            DoAddItemToList(list, size);
            return 0;
        }
    }
    cout << "Enter item price and discount rate: ";
    cin >> price >> discountRate;
    CItem itemlist(code, description, price, discountRate);
    list[size] = itemlist;
    return 0;
}
 
void DoSetItemPrice(CItem list[], int size)
{
    int tempcode;
    double tempPrice;
    cout << "------------------------------------------------\n";
    cout << "Please enter the item code of an item";
    cin >> tempcode;
 
    for (int i = 0; i < size; i++){
 
        if (list[i].HasCode(tempcode)){
            cout << "Please enter the new price for " << list[i].GetDescription();
            cin >> tempPrice;
            list[i].SetPrice(tempPrice);
        }
    }
    cout << "------------------------------------------------\n";
}
 
 
void DoSetItemDiscountRate(CItem list[], int size)
{
    int code; double discount;
    cout << "Enter item code & new discount rate:"; cin >> code >> discount;
    for (int i = 0; i < size; i++) {
        if (list[i].HasCode(code))
            list[i].SetDiscountRate(discount);
    }
}
 
void DoDisplayItem(CItem list[], int size)
{
    int tempcode;
    cout << "------------------------------------------------\n";
 
    cout << "Please enter an item code: \n";
    cin >> tempcode;
 
    for (int i = 0; i < size; i++){
 
        if (list[i].HasCode(tempcode))
            list[i].Display();
    }
    cout << "------------------------------------------------\n";
}
 
void DoOrderCost(CItem list[], int size)
{
    int tempcode, tempquantity;
    double cost;
    string dec;
    cout << "------------------------------------------------\n";
 
    cout << "PLease enter an item code followed by the quantity :";
    cin >> tempcode >> tempquantity;
 
    for (int i = 0; i < size; i++){
 
        if (list[i].HasCode(tempcode)) {
            //Calculates the amount saved if the discount is applied
            cout << "Will this customer be given the discount of " << setprecision(2) << fixed << (tempquantity * list[i].GetDiscount()); 
            cout << "e for their " << list[i].GetDescription() << "?(Yes/No)\n";
            cin >> dec;
        //Gives the customer choice whether they want to apply it or not
            if (dec == "Yes" || dec == "yes"){
                cost = (list[i].GetPrice()*tempquantity) - (tempquantity * list[i].GetDiscount());
                cout << "The cost of the order is " << cost << endl;
            }
            else if (dec == "No" || dec == "no"){
                cost = list[i].GetPrice() * tempquantity;
                cout << "The cost of the order is " << cost << endl;
            }
            else{
                cout << "Invalid option";
            }
        }
    }
    cout << "------------------------------------------------\n";
}
 
void DoTotalInvoice(CItem list[], int size)
{
    //Calculate the total price of all items in Object Array
    double total = 0;
    DoDisplayFullPriceList(list, size);
    cout << endl;
 
    for (int i = 0; i < size; i++)
        total += list[i].GetPrice();
    
    cout << "The total price of the collection is " << total << endl;
    cout << "------------------------------------------------\n";
}
 
void DoRemoveItemFromList(CItem list[], int size)
{
    int found = 0, itemCode;
    char confirmation = 'x';
    //finds the itemcode of product that's being deleted
    cout << "Enter item code: "; cin >> itemCode;
    for (int i = 0; i < size; i++) {
        if (list[i].HasCode(itemCode)) {
            found = i;
            found++;
            break;
        }
    }
    if (found == 0) {
        cout << "Item doesn't exist, try deleting an item that exists.\n";
        DoRemoveItemFromList(list, size);
    }
    //removes product, they pushes every other item that was ahead of that item one slot back
        for (int j = found; j < size; j++) {
            int code = list[j].GetCode();
            string description = list[j].GetDescription();
            double price = list[j].GetPrice();
            double discount = list[j].GetDiscountRate();
 
            CItem templist(code, description, price, discount);
            list[j - 1] = templist;
        }
 
    
}
 
void DoQuit(CItem list[], int size)
{
    WriteItemFile(list, size);
    cout << "Writing contents to file";
    for (int i = 0; i < 3; i++) {
        Sleep(400);
        cout << ".";
    }
    cout << endl << "File transfer completed\n";
}
 
void DoAddUser(CUser list[], int userlistSize)
{
    string username;
    int password = 0;
    char userType = 0;
    cout << "Please enter your username, password, user type: ";
    cin >> username >> password >> userType;
    CUser templist(username, password, userType);
    list[userlistSize] = templist;
}
 
void DoEditUser(CUser userlist[], int userlistSize)
{
    string userName;
 
    cout << "Please enter username: ";
    cin >> userName;
 
 
 
    int newPassword;
 
    for (int i = 0; i < userlistSize; i++) {
 
        if (userName == userlist[i].GetUsername()) {
            cout << "Please enter new password: ";
            cin >> newPassword;
 
            userlist[i].SetPassword(newPassword);
        }
    }
 
    cout << "Your password has been changed.";
 
}
 
int ManagerMenu(CItem list[], int itemlistSize, CUser userlist[], int userlistSize)
{
    int input = 1;
 
    
 
    while (input != 0) {
 
        cout << "\t     ItemMenu\n";
        cout << "\t1.\tInitialize Price List\n";
        cout << "\t2.\tDisplay Full Price List\n";
        cout << "\t3.\tAdd Item to List\n";
        cout << "\t4.\tSet Item Price\n";
        cout << "\t5.\tSet Item Discount Rate\n";
        cout << "\t6.\tDisplay Item\n";
        cout << "\t7.\tOrder Cost\n";
        cout << "\t8.\tTotal Invoice\n";
        cout << "\t9.\tRemove Item from List\n";
        cout << "\t10.\tAdd User\n";
        cout << "\t11.\tEdit User\n";
        cout << "\t12.\tSave & Log out\n";
        cout << "\t0.\tSave & Quit\n";
 
 
        cout << "\nChoose an option\n";
        cin >> input;
 
        switch (input) {
        case 1:
            itemlistSize = DoInitializePriceList(list, itemlistSize);
            cout << itemlistSize;
            break;
        case 2:
            DoDisplayFullPriceList(list, itemlistSize);
            break;
        case 3:
            DoAddItemToList(list, itemlistSize);
            itemlistSize++;
            break;
        case 4:
            DoSetItemPrice(list, itemlistSize);
            break;
        case 5:
            DoSetItemDiscountRate(list, itemlistSize);
            break;
        case 6:
            DoDisplayItem(list, itemlistSize);
            break;
        case 7:
            DoOrderCost(list, itemlistSize);
            break;
        case 8:
            DoTotalInvoice(list, itemlistSize);
            break;
        case 9:
            DoRemoveItemFromList(list, itemlistSize);
            itemlistSize--;
            break;
        case 10:
            DoAddUser(userlist, userlistSize);
            userlistSize++;
            break;
        case 11:
            DoEditUser(userlist, userlistSize);
            break;
        case 12:
            DoQuit(list, itemlistSize);
            WriteUserFile(userlist, userlistSize);
            system("cls");
            Login(userlist, userlistSize, list, itemlistSize);
            return 0;
            break;
        case 0:
            DoQuit(list, itemlistSize);
            WriteUserFile(userlist, userlistSize);
            break;
        default:
            break;
        }
    }
    return 0;
 
}
 
int AssistantMenu(CItem list[], int actualSize, CUser userlist[], int userlistSize)
{
    int input = 1;
 
    
 
    while (input != 0) {
 
        cout << "\t     ItemMenu\n";
        cout << "\t1.\tDisplay Full Price List\n";
        cout << "\t2.\tSet Item Price\n";
        cout << "\t3.\tSet Item Discount Rate\n";
        cout << "\t4.\tDisplay Item\n";
        cout << "\t5.\tOrder Cost\n";
        cout << "\t6.\tTotal Invoice\n";
        cout << "\t7.\tSave & Log off\n";
        cout << "\t0.\tSave & Quit\n";
        cout << "\nChoose an option\n";
        cin >> input;
 
        switch (input) {
 
        case 1:
            DoDisplayFullPriceList(list, actualSize);
            break;
        case 2:
            DoSetItemPrice(list, actualSize);
            break;
        case 3:
            DoSetItemDiscountRate(list, actualSize);
            break;
        case 4:
            DoDisplayItem(list, actualSize);
            break;
        case 5:
            DoOrderCost(list, actualSize);
            break;
        case 6:
            DoTotalInvoice(list, actualSize);
            break;
        case 7:
            DoQuit(list, actualSize);
            system("cls");
            Login(userlist, userlistSize, list, actualSize);
            return 0;
            break;
        case 0:
            DoQuit(list, actualSize);
            break;
        default:
            break;
        }
    }
    return 0;
}
```

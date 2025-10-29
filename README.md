#include <iostream>
#include <vector>
#include <iomanip>
using namespace std;

// 🔹 Abstract Base Class: Vehicle
class Vehicle {
protected:
    string number;
    string ownerName;

public:
    Vehicle(string n, string o) : number(n), ownerName(o) {}
    virtual void displayInfo() = 0;               // Abstraction
    virtual double calculateFee(int hours) = 0;   // Polymorphism
    virtual ~Vehicle() {}
};

// 🔹 Derived Class: Car
class Car : public Vehicle {
public:
    Car(string n, string o) : Vehicle(n, o) {}
    void displayInfo() override {
        cout << "🚗 Car | Number: " << number << " | Owner: " << ownerName << endl;
    }
    double calculateFee(int hours) override {
        return hours * 30; // Rs.30 per hour
    }
};

// 🔹 Derived Class: Bike
class Bike : public Vehicle {
public:
    Bike(string n, string o) : Vehicle(n, o) {}
    void displayInfo() override {
        cout << "🏍️  Bike | Number: " << number << " | Owner: " << ownerName << endl;
    }
    double calculateFee(int hours) override {
        return hours * 15; // Rs.15 per hour
    }
};

// 🔹 Parking Slot Class
class ParkingSlot {
    int slotNumber;
    bool isOccupied;
    Vehicle* parkedVehicle;

public:
    ParkingSlot(int num) : slotNumber(num), isOccupied(false), parkedVehicle(nullptr) {}

    bool getStatus() { return isOccupied; }

    void parkVehicle(Vehicle* v) {
        if (!isOccupied) {
            parkedVehicle = v;
            isOccupied = true;
            cout << "✅ Vehicle parked successfully in slot " << slotNumber << endl;
        } else {
            cout << "❌ Slot " << slotNumber << " is already occupied!" << endl;
        }
    }

    void removeVehicle(int hours) {
        if (isOccupied) {
            double fee = parkedVehicle->calculateFee(hours);
            cout << "💰 Parking Fee: Rs." << fee << endl;
            cout << "🚗 Vehicle removed from slot " << slotNumber << endl;
            delete parkedVehicle;
            parkedVehicle = nullptr;
            isOccupied = false;
        } else {
            cout << "⚠️ Slot " << slotNumber << " is already empty!" << endl;
        }
    }

    void showSlotInfo() {
        cout << setw(6) << "Slot " << slotNumber << ": ";
        if (isOccupied) {
            parkedVehicle->displayInfo();
        } else {
            cout << "🅿️  Empty" << endl;
        }
    }
};

// 🔹 Parking Lot Class
class ParkingLot {
    vector<ParkingSlot> slots;

public:
    ParkingLot(int n) {
        for (int i = 1; i <= n; i++) {
            slots.push_back(ParkingSlot(i));
        }
    }

    void displaySlots() {
        cout << "\n==================== PARKING LOT STATUS ====================\n";
        for (auto &slot : slots) {
            slot.showSlotInfo();
        }
        cout << "============================================================\n";
    }

    void park(int slotNum, Vehicle* v) {
        if (slotNum > 0 && slotNum <= slots.size()) {
            slots[slotNum - 1].parkVehicle(v);
        } else {
            cout << "❌ Invalid Slot Number!" << endl;
        }
    }

    void remove(int slotNum, int hours) {
        if (slotNum > 0 && slotNum <= slots.size()) {
            slots[slotNum - 1].removeVehicle(hours);
        } else {
            cout << "❌ Invalid Slot Number!" << endl;
        }
    }
    // 🔹 Add this function inside ParkingLot class
void showLiveMap() {
    cout << "\n🗺️  LIVE PARKING SLOT MAP:\n";
    for (int i = 0; i < slots.size(); i++) {
        cout << "Slot " << i + 1 << ": ";
        if (!slots[i].getStatus())
            cout << "🅿️ [ ] "; // empty
        else
            cout << "[X] ";   // occupied
        cout << "  ";
    }
    cout << "\n";
}

};

// 🔹 MAIN FUNCTION
int main() {
    cout << "=============================================\n";
    cout << "🚘  SMART PARKING LOT SYSTEM 🚘\n";
    cout << "=============================================\n\n";

    int totalSlots;
    cout << "Enter total number of parking slots: ";
    cin >> totalSlots;

    ParkingLot lot(totalSlots);
    int choice;

    do {
        cout << "\n\n---------- MENU ----------\n";
        cout << "1. Park Vehicle\n";
        cout << "2. Remove Vehicle\n";
        cout << "3. Display Parking Slots\n";
        cout << "4. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
        case 1: {
            int slotNum, type;
            string num, owner;
            cout << "Enter slot number: ";
            cin >> slotNum;
            cout << "Enter vehicle number: ";
            cin >> num;
            cout << "Enter owner name: ";
            cin >> owner;
            cout << "Select Vehicle Type (1-Car / 2-Bike): ";
            cin >> type;

            Vehicle* v = nullptr;
            if (type == 1)
                v = new Car(num, owner);
            else if (type == 2)
                v = new Bike(num, owner);
            else {
                cout << "Invalid type!" << endl;
                break;
            }

            lot.park(slotNum, v);
            lot.showLiveMap();

            break;
        }

        case 2: {
            int slotNum, hours;
            cout << "Enter slot number to remove vehicle: ";
            cin >> slotNum;
            cout << "Enter number of parking hours: ";
            cin >> hours;
            lot.remove(slotNum, hours);
            lot.showLiveMap();

            break;
        }

        case 3:
            lot.displaySlots();
            break;

        case 4:
            cout << "\nThank you for using Smart Parking Lot System! 👋\n";
            break;

        default:
            cout << "Invalid choice! Please try again.\n";
        }
    } while (choice != 4);

    return 0;
}

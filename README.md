# Import necessary modules
import random

# Global dictionaries to store users and bookings
users = {}
bookings = {}

# Class for User Management
class User:
    def __init__(self, username, password):
        self.username = username
        self.password = password

    def authenticate(self, username, password):
        return self.username == username and self.password == password


# Function to create a new user
def create_user():
    global users
    username = input("\nEnter new username: ")

    if username in users:
        print("❌ Username already exists!")
        return False

    password = input("Enter new password: ")
    users[username] = User(username, password)
    bookings[username] = []  # Initialize empty booking list for new user
    print("✅ User created successfully!")
    return True


# Function to log in
def login():
    username = input("\nEnter username: ")
    password = input("Enter password: ")

    if username in users and users[username].authenticate(username, password):
        print(f"✅ Welcome back, {username}!")
        return username
    else:
        print("❌ Invalid credentials!")
        return None


# Function to book an appointment
def book_appointment(username):
    date = input("\nEnter appointment date (YYYY-MM-DD): ")
    time = input("Enter appointment time (HH:MM): ")
    description = input("Enter description: ")

    appointment = {
        "id": random.randint(1000, 9999),
        "date": date,
        "time": time,
        "description": description,
    }

    bookings[username].append(appointment)
    print(f"✅ Appointment booked for {date} at {time}!")


# Function to cancel an appointment
def cancel_appointment(username):
    if not bookings[username]:
        print("❌ No appointments to cancel!")
        return

    print("\nYour Appointments:")
    for i, appointment in enumerate(bookings[username], 1):
        print(f"{i}. {appointment['date']} at {appointment['time']} - {appointment['description']}")

    try:
        choice = int(input("\nEnter appointment number to cancel: ")) - 1
        assert 0 <= choice < len(bookings[username]), "Invalid appointment number!"

        removed = bookings[username].pop(choice)
        print(f"✅ Appointment on {removed['date']} at {removed['time']} canceled!")

    except (ValueError, AssertionError) as e:
        print("❌ Error:", e)


# Function to list appointments
def list_appointments(username):
    if not bookings[username]:
        print("\n📌 No appointments available.")
        return

    print("\nYour Appointments:")
    for i, appointment in enumerate(bookings[username], 1):
        print(f"{i}. {appointment['date']} at {appointment['time']} - {appointment['description']}")


# Function to save bookings to a file
def save_bookings():
    with open("bookings.txt", "w") as file:
        file.write(str(bookings))
    print("\n💾 Bookings saved successfully!")


# Main Execution
if __name__ == "__main__":
    print("📌 Welcome to Appointment Booking System!")

    while True:
        print("\n1. Register\n2. Login\n3. Exit")
        choice = input("Choose an option: ")

        if choice == "1":
            create_user()
        elif choice == "2":
            user = login()
            if user:
                while True:
                    print("\n--- Booking Menu ---")
                    print("1. Book Appointment\n2. Cancel Appointment\n3. List Appointments\n4. Save Bookings\n5. Logout")
                    option = input("Choose an option: ")

                    if option == "1":
                        book_appointment(user)
                    elif option == "2":
                        cancel_appointment(user)
                    elif option == "3":
                        list_appointments(user)
                    elif option == "4":
                        save_bookings()
                    elif option == "5":
                        print("👋 Logging out...\n")
                        break
                    else:
                        print("❌ Invalid option!")
        elif choice == "3":
            print("👋 Goodbye!")
            break
        else:
            print("❌ Invalid option!")

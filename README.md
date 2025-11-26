import json, os, re
from datetime import datetime

FILE = "visitors.json"

def load():
    if not os.path.exists(FILE):    
        with open(FILE, "w") as f:
            json.dump([], f)
    with open(FILE, "r") as f:
        return json.load(f)

def save(data):
    with open(FILE, "w") as f:
        json.dump(data, f, indent=4)

def valid_word(text):
    return bool(re.match("^[A-Za-z ]+$", text))

def login():
    print("\nðŸ” ADMIN LOGIN")
    username = "admin"
    password = "1234"
    for attempt in range(3):
        user = input("Username: ").strip()
        pin = input("Password: ").strip()
        if user == username and pin == password:
            print("âœ… Login successful! Welcome, admin.")
            return True
        else:
            print("âŒ Incorrect username or password.")
    print("ðŸš« Too many failed attempts. System locked.")
    return False

def add_visitor(data):
    print("\nðŸ“ ADD VISITOR")
    while True:
        name = input("Visitor Name: ").strip()
        if not valid_word(name):
            print("âŒ Name must contain letters only.")
            continue
        break
    while True:
        purpose = input("Purpose of Visit: ").strip()
        if not valid_word(purpose):
            print("âŒ Purpose must contain letters only.")
            continue
        break
    person = input("Person to Visit: ").strip()
    time_in = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    visitor = {
        "name": name,
        "purpose": purpose,
        "person": person,
        "time_in": time_in,
        "time_out": None
    }
    data.append(visitor)
    save(data)
    print(f"âœ… {name} logged in at {time_in}")

def mark_exit(data):
    print("\nðŸšª MARK VISITOR EXIT")
    name = input("Enter visitor name: ").strip()
    for v in data:
        if v["name"].lower() == name.lower() and v["time_out"] is None:
            v["time_out"] = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            save(data)
            print(f"âœ… {name} logged out at {v['time_out']}")
            return
    print("âŒ Visitor not found or already exited.")

def view_visitors(data):
    if not data:
        print("\nNo visitor records yet.")
        return
    print("\nðŸ“‹ VISITOR LOG BOOK")
    for v in data:
        print("----------------------------")
        print(f"Name: {v['name']}")
        print(f"Purpose: {v['purpose']}")
        print(f"Person to Visit: {v['person']}")
        print(f"Time In: {v['time_in']}")
        print(f"Time Out: {v['time_out'] if v['time_out'] else 'Still Inside'}")
    print("----------------------------")

def main_menu(data):
    while True:
        print("\nðŸ« SCHOOL VISITOR LOG BOOK SYSTEM")
        print("1. Add Visitor")
        print("2. Mark Visitor Exit")
        print("3. View All Visitors")
        print("4. Exit")
        choice = input("Choose option: ").strip()

        if choice == "1":
            add_visitor(data)
        elif choice == "2":
            mark_exit(data)
        elif choice == "3":
            view_visitors(data)
        elif choice == "4":
            print("ðŸ‘‹ Bye... Data saved.")
            save(data)
            break
        else:
            print("âŒ Invalid choice. Please enter 1â€“4.")

def main():
    print("=== SCHOOL VISITOR LOG BOOK SYSTEM ===")
    if not login():
        return
    data = load()
    main_menu(data)

if __name__ == "__main__":
    main()

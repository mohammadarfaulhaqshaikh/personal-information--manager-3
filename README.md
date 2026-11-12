import json
import os
import csv

# File to store contacts data
DATA_FILE = "contacts_data.json"

# Developer Details (Aapki Details)
DEV_NAME = "Shaikh Arfa"
DEV_EMAIL = "arfask@gmail.com"
DEV_ADDRESS = "basmath"
DEV_PHONE = "1234567890"

def load_contacts():
    """Load contacts from JSON file if it exists, or initialize with a default contact"""
    if os.path.exists(DATA_FILE):
        try:
            with open(DATA_FILE, "r") as file:
                print("✔ Loaded existing contacts file.")
                return json.load(file)
        except:
            print("⚠ Error reading file. Starting with empty contacts.")
            return {}
    else:
        print("✔ No existing contacts file found. Adding your profile as initial contact.")
        # Default starting contact using your details
        initial_contacts = {
            DEV_NAME: {
                "phone": DEV_PHONE,
                "email": DEV_EMAIL,
                "address": DEV_ADDRESS,
                "group": "Personal"
            }
        }
        save_contacts(initial_contacts)
        return initial_contacts

def save_contacts(contacts):
    """Save contacts dictionary to JSON file"""
    try:
        with open(DATA_FILE, "w") as file:
            json.dump(contacts, file, indent=4)
        print("✔ Contacts saved successfully!")
    except:
        print("❌ Error saving contacts to file.")

def add_contact(contacts):
    """Add a new contact"""
    print("\n--- ADD NEW CONTACT ---")
    name = input("Enter contact name: ").strip()
    
    if not name:
        print("❌ Name cannot be empty!")
        return

    if name in contacts:
        print(f"⚠ Contact '{name}' already exists!")
        choice = input("Do you want to update it? (y/n): ").lower()
        if choice == 'y':
            update_contact(contacts, name)
        return

    phone = input("Enter phone number: ").strip()
    email = input("Enter email (optional, press Enter to skip): ").strip()
    address = input("Enter address (optional, press Enter to skip): ").strip()
    group = input("Enter group (Friends/Work/Family/Other): ").strip()

    contacts[name] = {
        "phone": phone,
        "email": email,
        "address": address,
        "group": group if group else "General"
    }
    
    print(f"✔ Contact '{name}' added successfully!")
    save_contacts(contacts)

def search_contact(contacts):
    """Search for a contact by name"""
    print("\n--- SEARCH CONTACT ---")
    search_term = input("Enter name to search: ").strip().lower()
    
    results = {}
    for name, info in contacts.items():
        if search_term in name.lower():
            results[name] = info

    if not results:
        print("❌ No contacts found.")
        return

    print(f"\nFound {len(results)} contact(s):")
    print("-" * 40)
    for name, info in results.items():
        print(f"👤 Name: {name}")
        print(f"   📞 Phone: {info['phone']}")
        if info['email']:
            print(f"   📧 Email: {info['email']}")
        if info['address']:
            print(f"   🏠 Address: {info['address']}")
        print(f"   📂 Group: {info['group']}")
        print("-" * 40)

def update_contact(contacts, target_name=None):
    """Update an existing contact"""
    print("\n--- UPDATE CONTACT ---")
    if not target_name:
        target_name = input("Enter the name of the contact to update: ").strip()

    if target_name not in contacts:
        print(f"❌ Contact '{target_name}' not found!")
        return

    print(f"Updating contact: {target_name}")
    phone = input(f"Enter new phone [{contacts[target_name]['phone']}]: ").strip()
    email = input(f"Enter new email [{contacts[target_name]['email']}]: ").strip()
    address = input(f"Enter new address [{contacts[target_name]['address']}]: ").strip()
    group = input(f"Enter new group [{contacts[target_name]['group']}]: ").strip()

    if phone:
        contacts[target_name]['phone'] = phone
    if email:
        contacts[target_name]['email'] = email
    if address:
        contacts[target_name]['address'] = address
    if group:
        contacts[target_name]['group'] = group

    print(f"✔ Contact '{target_name}' updated successfully!")
    save_contacts(contacts)

def delete_contact(contacts):
    """Delete a contact"""
    print("\n--- DELETE CONTACT ---")
    name = input("Enter the name of the contact to delete: ").strip()

    if name not in contacts:
        print(f"❌ Contact '{name}' not found!")
        return

    confirm = input(f"Are you sure you want to delete '{name}'? (y/n): ").lower()
    if confirm == 'y':
        del contacts[name]
        print(f"✔ Contact '{name}' deleted successfully!")
        save_contacts(contacts)
    else:
        print("Deletion cancelled.")

def display_all(contacts):
    """View all contacts"""
    if not contacts:
        print("\n❌ No contacts available.")
        return

    print(f"\n--- ALL CONTACTS ({len(contacts)} total) ---")
    print("=" * 40)
    for name, info in contacts.items():
        print(f"👤 {name}")
        print(f"   📞 {info['phone']}")
        if info['email']:
            print(f"   📧 {info['email']}")
        if info['address']:
            print(f"   🏠 {info['address']}")
        print(f"   📂 {info['group']}")
        print("-" * 40)

def export_to_csv(contacts):
    """Export contacts to a CSV file"""
    if not contacts:
        print("\n❌ No contacts to export.")
        return
    
    filename = "contacts_exported.csv"
    try:
        with open(filename, mode='w', newline='', encoding='utf-8') as file:
            writer = csv.writer(file)
            writer.writerow(["Name", "Phone", "Email", "Address", "Group"])
            for name, info in contacts.items():
                writer.writerow([name, info['phone'], info['email'], info['address'], info['group']])
        print(f"✔ Contacts successfully exported to {filename}!")
    except:
        print("❌ Error exporting to CSV.")

def view_statistics(contacts):
    """View statistics like total contacts and group breakdown"""
    print("\n--- CONTACT STATISTICS ---")
    total = len(contacts)
    print(f"Total Contacts: {total}")

    if total > 0:
        groups = {}
        for info in contacts.values():
            grp = info['group']
            groups[grp] = groups.get(grp, 0) + 1

        print("\nContacts by Group:")
        for grp, count in groups.items():
            print(f"  - {grp}: {count} contact(s)")

def main():
    """Main menu function"""
    contacts = load_contacts()

    while True:
        print("\n" + "=" * 40)
        print("       CONTACT MANAGEMENT SYSTEM")
        print(f"       Developer: {DEV_NAME}")
        print("=" * 40)
        print("1. Add New Contact")
        print("2. Search Contact")
        print("3. Update Contact")
        print("4. Delete Contact")
        print("5. View All Contacts")
        print("6. Export to CSV")
        print("7. View Statistics")
        print("8. Exit")
        print("=" * 40)

        choice = input("Enter your choice (1-8): ").strip()

        if choice == '1':
            add_contact(contacts)
        elif choice == '2':
            search_contact(contacts)
        elif choice == '3':
            update_contact(contacts)
        elif choice == '4':
            delete_contact(contacts)
        elif choice == '5':
            display_all(contacts)
        elif choice == '6':
            export_to_csv(contacts)
        elif choice == '7':
            view_statistics(contacts)
        elif choice == '8':
            print(f"\nThank you for using Contact Management System!")
            print(f"Developed by: {DEV_NAME} ({DEV_EMAIL})")
            break
        else:
            print("❌ Invalid choice! Please enter a number between 1 and 8.")

if __name__ == "__main__":
    main()

  

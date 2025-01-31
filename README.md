```python
import tkinter as tk
from tkinter import messagebox
from tkinter import filedialog

class PhoneBookApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Телефонная книга")

        # Устанавливаем размер окна 800x600
        self.root.geometry("800x600")

        self.contacts = {}

        # Элементы интерфейса
        self.name_label = tk.Label(root, text="Имя:")
        self.name_label.grid(row=0, column=0, padx=10, pady=10, sticky="w")

        self.surname_label = tk.Label(root, text="Фамилия:")
        self.surname_label.grid(row=1, column=0, padx=10, pady=10, sticky="w")

        self.phone_label = tk.Label(root, text="Телефон:")
        self.phone_label.grid(row=2, column=0, padx=10, pady=10, sticky="w")

        self.name_entry = tk.Entry(root, width=30)
        self.name_entry.grid(row=0, column=1, padx=10, pady=10)

        self.surname_entry = tk.Entry(root, width=30)
        self.surname_entry.grid(row=1, column=1, padx=10, pady=10)

        self.phone_entry = tk.Entry(root, width=30)
        self.phone_entry.grid(row=2, column=1, padx=10, pady=10)

        # Кнопки
        self.add_button = tk.Button(root, text="Добавить контакт", command=self.add_contact, width=20)
        self.add_button.grid(row=3, column=0, padx=10, pady=10)

        self.delete_button = tk.Button(root, text="Удалить контакт", command=self.delete_contact, width=20)
        self.delete_button.grid(row=3, column=1, padx=10, pady=10)

        self.search_button = tk.Button(root, text="Поиск контакта", command=self.search_contact, width=20)
        self.search_button.grid(row=4, column=0, padx=10, pady=10)

        self.show_button = tk.Button(root, text="Показать все контакты", command=self.show_contacts, width=20)
        self.show_button.grid(row=4, column=1, padx=10, pady=10)

        self.save_button = tk.Button(root, text="Сохранить в файл", command=self.save_to_file, width=20)
        self.save_button.grid(row=5, column=0, padx=10, pady=10)

        self.load_button = tk.Button(root, text="Загрузить из файла", command=self.load_from_file, width=20)
        self.load_button.grid(row=5, column=1, padx=10, pady=10)

        # Контейнер Frame для Listbox с отступами
        self.listbox_frame = tk.Frame(root)
        self.listbox_frame.grid(row=6, column=0, columnspan=2, padx=10, pady=20)

        # Поле для отображения контактов с отступами
        self.contact_listbox = tk.Listbox(self.listbox_frame, width=50, height=10)
        self.contact_listbox.grid(row=0, column=0, padx=10, pady=10)

    def add_contact(self):
        """Добавление нового контакта в телефонную книгу."""
        name = self.name_entry.get()
        surname = self.surname_entry.get()
        phone = self.phone_entry.get()

        if name and surname and phone:
            contact_id = f"{surname} {name}"
            self.contacts[contact_id] = {'name': name, 'surname': surname, 'phone': phone}
            self.name_entry.delete(0, tk.END)
            self.surname_entry.delete(0, tk.END)
            self.phone_entry.delete(0, tk.END)
            messagebox.showinfo("Информация", f"Контакт {name} {surname} добавлен.")
        else:
            messagebox.showinfo("Ошибка", "Заполните все поля!")

    def delete_contact(self):
        """Удаление контакта по фамилии и имени."""
        name = self.name_entry.get()
        surname = self.surname_entry.get()

        if name and surname:
            contact_id = f"{surname} {name}"
            if contact_id in self.contacts:
                del self.contacts[contact_id]
                self.name_entry.delete(0, tk.END)
                self.surname_entry.delete(0, tk.END)
                self.phone_entry.delete(0, tk.END)
                messagebox.showinfo("Информация", f"Контакт {name} {surname} удален.")
            else:
                messagebox.showinfo("Ошибка", f"Контакт с именем {name} {surname} не найден.")
        else:
            messagebox.showinfo("Ошибка", "Введите фамилию и имя для удаления!")

    def search_contact(self):
        """Поиск контакта по имени, фамилии или телефону."""
        search_term = self.name_entry.get() or self.surname_entry.get()

        if search_term:
            search_results = []
            for contact_id, details in self.contacts.items():
                if search_term.lower() in contact_id.lower() or search_term.lower() in details['phone'].lower():
                    search_results.append(f"{contact_id}: {details['phone']}")
            
            if search_results:
                self.contact_listbox.delete(0, tk.END)
                for result in search_results:
                    self.contact_listbox.insert(tk.END, result)
            else:
                messagebox.showinfo("Результат поиска", "Контакт не найден.")
        else:
            messagebox.showinfo("Ошибка", "Введите фамилию или имя для поиска!")
            messagebox.showinfo("Ошибка", "Введите фамилию или имя для поиска!")

    def show_contacts(self):
        """Отображение всех контактов."""
        self.contact_listbox.delete(0, tk.END)
        for contact_id, details in self.contacts.items():
            self.contact_listbox.insert(tk.END, f"{contact_id}: {details['phone']}")

    def save_to_file(self):
        """Сохранение всех контактов в текстовый файл."""
        file_name = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("Text Files", "*.txt")])
        if file_name:
            with open(file_name, 'w') as file:
                for contact_id, details in self.contacts.items():
                    # Запись контактов в файл: фамилия, имя, телефон
                    file.write(f"{contact_id},{details['name']},{details['surname']},{details['phone']}\n")
            messagebox.showinfo("Информация", "Контакты успешно сохранены в файл.")

    def load_from_file(self):
        """Загрузка контактов из текстового файла."""
        file_name = filedialog.askopenfilename(filetypes=[("Text Files", "*.txt")])
        if file_name:
            try:
                self.contacts.clear()  # Очищаем текущий список контактов
                with open(file_name, 'r') as file:
                    for line in file:
                        # Разделяем строку на фамилию, имя и телефон
                        surname, name, phone = line.strip().split(',')
                        contact_id = f"{surname} {name}"
                        self.contacts[contact_id] = {'name': name, 'surname': surname, 'phone': phone}
                self.show_contacts()  # Обновляем отображение контактов
                messagebox.showinfo("Информация", "Контакты успешно загружены из файла.")
            except Exception as e:
                messagebox.showinfo("Ошибка", f"Произошла ошибка: {e}")

# Запуск приложения
if __name__ == "__main__":
    root = tk.Tk()
    app = PhoneBookApp(root)
    root.mainloop()

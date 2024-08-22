import json
import os

class Task:
    def __init__(self, title, description):
        self.title = title
        self.description = description
        self.completed = False

    def mark_as_completed(self):
        self.completed = True

    def __str__(self):
        status = "Completed" if self.completed else "Pending"
        return f"Task: {self.title}\nDescription: {self.description}\nStatus: {status}"

class TaskManager:
    def __init__(self, filepath='tasks.json'):
        self.tasks = []
        self.filepath = filepath
        self.load_tasks()

    def add_task(self, title, description):
        task = Task(title, description)
        self.tasks.append(task)
        self.save_tasks()

    def list_tasks(self):
        if not self.tasks:
            print("No tasks available.")
            return
        for index, task in enumerate(self.tasks, 1):
            print(f"\nTask {index}:\n{task}")

    def mark_task_as_completed(self, index):
        if 0 <= index < len(self.tasks):
            self.tasks[index].mark_as_completed()
            self.save_tasks()
            print(f"Task {index + 1} marked as completed.")
        else:
            print("Invalid task index.")

    def save_tasks(self):
        with open(self.filepath, 'w') as file:
            json.dump([task.__dict__ for task in self.tasks], file)

    def load_tasks(self):
        if os.path.exists(self.filepath):
            with open(self.filepath, 'r') as file:
                task_dicts = json.load(file)
                self.tasks = [Task(**task_dict) for task_dict in task_dicts]

    def delete_task(self, index):
        if 0 <= index < len(self.tasks):
            deleted_task = self.tasks.pop(index)
            self.save_tasks()
            print(f"Task '{deleted_task.title}' deleted.")
        else:
            print("Invalid task index.")

    def delete_all_tasks(self):
        self.tasks = []
        self.save_tasks()
        print("All tasks have been deleted.")

def main():
    manager = TaskManager()

    while True:
        print("\nTask Manager")
        print("1. Add Task")
        print("2. List Tasks")
        print("3. Mark Task as Completed")
        print("4. Delete Task")
        print("5. Delete All Tasks")
        print("6. Exit")
        choice = input("Choose an option: ")

        if choice == '1':
            title = input("Enter task title: ")
            description = input("Enter task description: ")
            manager.add_task(title, description)
            print("Task added successfully.")
        elif choice == '2':
            manager.list_tasks()
        elif choice == '3':
            task_index = int(input("Enter task number to mark as completed: ")) - 1
            manager.mark_task_as_completed(task_index)
        elif choice == '4':
            task_index = int(input("Enter task number to delete: ")) - 1
            manager.delete_task(task_index)
        elif choice == '5':
            confirmation = input("Are you sure you want to delete all tasks? (yes/no): ")
            if confirmation.lower() == 'yes':
                manager.delete_all_tasks()
        elif choice == '6':
            print("Exiting Task Manager.")
            break
        else:
            print("Invalid choice. Please choose a valid option.")

if __name__ == "__main__":
    main()

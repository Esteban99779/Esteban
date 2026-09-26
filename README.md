import tkinter as tk
from tkinter import ttk, messagebox
from datetime import datetime


# ==========================================================
# CLASS: Usuario
# ==========================================================

class Usuario:
    def __init__(self, usuario, password):
        self._usuario = usuario
        self._password = password

    def validar(self, usuario_ingresado, password_ingresada):
        return (
            usuario_ingresado == self._usuario
            and password_ingresada == self._password
        )


# ==========================================================
# CLASS: BicicletaTaller
# ==========================================================

class BicicletaTaller:
    def __init__(self, serial, costo_por_hora):
        self._serial = serial
        self._hora_ingreso = None
        self._hora_salida = None
        self._costo_por_hora = costo_por_hora

    def registrar_ingreso(self, hora):
        self._hora_ingreso = hora

    def registrar_salida(self, hora):
        self._hora_salida = hora

    def calcular_total(self, hora_salida):
        if self._hora_ingreso is None:
            raise ValueError("The bicycle does not have an entry time.")

        if hora_salida <= self._hora_ingreso:
            raise ValueError(
                "The exit time must be later than the entry time."
            )

        self.registrar_salida(hora_salida)

        tiempo_transcurrido = hora_salida - self._hora_ingreso

        # Convert elapsed time to hours.
        horas = tiempo_transcurrido.total_seconds() / 3600

        # The workshop charges according to the elapsed time.
        total = horas * self._costo_por_hora

        return total

    def obtener_serial(self):
        return self._serial


# ==========================================================
# CLASS: BicycleWorkshopApp
# ==========================================================

class BicycleWorkshopApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Bicycle Workshop Management System")
        self.root.geometry("700x500")
        self.root.resizable(False, False)

        # Internal list of bicycles
        self._bicicletas = []

        # Login user
        self._usuario = Usuario("programacion", "programacion")

        self.show_login()

    # ======================================================
    # LOGIN WINDOW
    # ======================================================

    def show_login(self):
        self.clear_window()

        self.root.configure(bg="#EAF2F8")

        title = tk.Label(
            self.root,
            text="BICYCLE WORKSHOP",
            font=("Arial", 24, "bold"),
            bg="#EAF2F8",
            fg="#154360"
        )
        title.pack(pady=(60, 10))

        subtitle = tk.Label(
            self.root,
            text="System Login",
            font=("Arial", 16),
            bg="#EAF2F8",
            fg="#2874A6"
        )
        subtitle.pack(pady=(0, 30))

        login_frame = tk.Frame(
            self.root,
            bg="white",
            padx=30,
            pady=30
        )
        login_frame.pack()

        tk.Label(
            login_frame,
            text="Username:",
            font=("Arial", 12),
            bg="white"
        ).grid(row=0, column=0, sticky="w", pady=10)

        self.username_entry = tk.Entry(
            login_frame,
            font=("Arial", 12),
            width=25
        )
        self.username_entry.grid(row=0, column=1, padx=10, pady=10)

        tk.Label(
            login_frame,
            text="Password:",
            font=("Arial", 12),
            bg="white"
        ).grid(row=1, column=0, sticky="w", pady=10)

        self.password_entry = tk.Entry(
            login_frame,
            font=("Arial", 12),
            width=25,
            show="*"
        )
        self.password_entry.grid(row=1, column=1, padx=10, pady=10)

        login_button = tk.Button(
            login_frame,
            text="LOGIN",
            font=("Arial", 12, "bold"),
            bg="#2874A6",
            fg="white",
            width=20,
            command=self.login
        )
        login_button.grid(
            row=2,
            column=0,
            columnspan=2,
            pady=20
        )

        self.password_entry.bind("<Return>", lambda event: self.login())

        self.username_entry.focus()

    # ======================================================
    # LOGIN VALIDATION
    # ======================================================

    def login(self):
        username = self.username_entry.get().strip()
        password = self.password_entry.get()

        if self._usuario.validar(username, password):
            messagebox.showinfo(
                "Successful Login",
                "Welcome to the Bicycle Workshop System."
            )
            self.show_main_window()
        else:
            messagebox.showerror(
                "Login Error",
                "Invalid username or password."
            )

            self.password_entry.delete(0, tk.END)
            self.password_entry.focus()

    # ======================================================
    # MAIN WINDOW
    # ======================================================

    def show_main_window(self):
        self.clear_window()

        self.root.configure(bg="#F4F6F7")

        title = tk.Label(
            self.root,
            text="BICYCLE WORKSHOP MANAGEMENT SYSTEM",
            font=("Arial", 20, "bold"),
            bg="#154360",
            fg="white",
            pady=15
        )
        title.pack(fill="x")

        # ----------------------------------------------
        # Registration section
        # ----------------------------------------------

        registration_frame = tk.LabelFrame(
            self.root,
            text="Register Bicycle",
            font=("Arial", 12, "bold"),
            bg="#F4F6F7",
            padx=20,
            pady=15
        )
        registration_frame.pack(
            fill="x",
            padx=25,
            pady=20
        )

        tk.Label(
            registration_frame,
            text="Serial Number:",
            font=("Arial", 11),
            bg="#F4F6F7"
        ).grid(row=0, column=0, padx=10, pady=10)

        self.serial_entry = tk.Entry(
            registration_frame,
            font=("Arial", 11),
            width=20
        )
        self.serial_entry.grid(
            row=0,
            column=1,
            padx=10,
            pady=10
        )

        tk.Label(
            registration_frame,
            text="Cost per Hour:",
            font=("Arial", 11),
            bg="#F4F6F7"
        ).grid(row=0, column=2, padx=10, pady=10)

        self.cost_entry = tk.Entry(
            registration_frame,
            font=("Arial", 11),
            width=15
        )
        self.cost_entry.grid(
            row=0,
            column=3,
            padx=10,
            pady=10
        )

        register_button = tk.Button(
            registration_frame,
            text="Register Bicycle",
            font=("Arial", 10, "bold"),
            bg="#239B56",
            fg="white",
            command=self.register_bicycle
        )
        register_button.grid(
            row=0,
            column=4,
            padx=10,
            pady=10
        )

        # ----------------------------------------------
        # Bicycle list
        # ----------------------------------------------

        list_frame = tk.LabelFrame(
            self.root,
            text="Registered Bicycles",
            font=("Arial", 12, "bold"),
            bg="#F4F6F7",
            padx=15,
            pady=15
        )
        list_frame.pack(
            fill="both",
            expand=True,
            padx=25,
            pady=5
        )

        columns = (
            "serial",
            "entry",
            "status"
        )

        self.bicycle_table = ttk.Treeview(
            list_frame,
            columns=columns,
            show="headings",
            height=8
        )

        self.bicycle_table.heading(
            "serial",
            text="Serial Number"
        )
        self.bicycle_table.heading(
            "entry",
            text="Entry Time"
        )
        self.bicycle_table.heading(
            "status",
            text="Status"
        )

        self.bicycle_table.column(
            "serial",
            width=180,
            anchor="center"
        )
        self.bicycle_table.column(
            "entry",
            width=220,
            anchor="center"
        )
        self.bicycle_table.column(
            "status",
            width=150,
            anchor="center"
        )

        self.bicycle_table.pack(
            fill="both",
            expand=True
        )

        # ----------------------------------------------
        # Exit section
        # ----------------------------------------------

        exit_frame = tk.Frame(
            self.root,
            bg="#F4F6F7"
        )
        exit_frame.pack(
            fill="x",
            padx=25,
            pady=15
        )

        exit_button = tk.Button(
            exit_frame,
            text="Register Exit",
            font=("Arial", 11, "bold"),
            bg="#E67E22",
            fg="white",
            width=20,
            command=self.register_exit
        )
        exit_button.pack(side="left", padx=10)

        logout_button = tk.Button(
            exit_frame,
            text="Logout",
            font=("Arial", 11, "bold"),
            bg="#C0392B",
            fg="white",
            width=15,
            command=self.logout
        )
        logout_button.pack(side="right", padx=10)

    # ======================================================
    # REGISTER BICYCLE
    # ======================================================

    def register_bicycle(self):
        serial = self.serial_entry.get().strip()
        cost_text = self.cost_entry.get().strip()

        if not serial:
            messagebox.showerror(
                "Input Error",
                "Please enter the bicycle serial number."
            )
            return

        if not cost_text:
            messagebox.showerror(
                "Input Error",
                "Please enter the cost per hour."
            )
            return

        try:
            cost = float(cost_text)

            if cost <= 0:
                raise ValueError

        except ValueError:
            messagebox.showerror(
                "Input Error",
                "The cost per hour must be a positive number."
            )
            return

        # Check duplicate serial numbers
        for bicycle in self._bicicletas:
            if bicycle.obtener_serial() == serial:
                messagebox.showerror(
                    "Registration Error",
                    "A bicycle with this serial number already exists."
                )
                return

        # Current date and time
        entry_time = datetime.now()

        bicycle = BicicletaTaller(
            serial,
            cost
        )

        bicycle.registrar_ingreso(entry_time)

        self._bicicletas.append(bicycle)

        self.update_table()

        self.serial_entry.delete(0, tk.END)
        self.cost_entry.delete(0, tk.END)

        messagebox.showinfo(
            "Registration Successful",
            f"Bicycle {serial} was registered successfully."
        )

    # ======================================================
    # UPDATE TABLE
    # ======================================================

    def update_table(self):
        for item in self.bicycle_table.get_children():
            self.bicycle_table.delete(item)

        for index, bicycle in enumerate(self._bicicletas):

            entry_time = bicycle._hora_ingreso

            if bicycle._hora_salida is None:
                status = "In Workshop"
            else:
                status = "Completed"

            self.bicycle_table.insert(
                "",
                "end",
                iid=str(index),
                values=(
                    bicycle.obtener_serial(),
                    entry_time.strftime("%Y-%m-%d %H:%M:%S"),
                    status
                )
            )

    # ======================================================
    # REGISTER EXIT
    # ======================================================

    def register_exit(self):
        selected = self.bicycle_table.selection()

        if not selected:
            messagebox.showwarning(
                "Selection Required",
                "Please select a bicycle first."
            )
            return

        index = int(selected[0])

        bicycle = self._bicicletas[index]

        if bicycle._hora_salida is not None:
            messagebox.showwarning(
                "Bicycle Already Completed",
                "This bicycle already has an exit time."
            )
            return

        # Current date and time
        exit_time = datetime.now()

        try:
            total = bicycle.calcular_total(exit_time)

        except ValueError as error:
            messagebox.showerror(
                "Time Error",
                str(error)
            )
            return

        self.update_table()

        messagebox.showinfo(
            "Service Completed",
            f"Bicycle: {bicycle.obtener_serial()}\n"
            f"Exit Time: {exit_time.strftime('%Y-%m-%d %H:%M:%S')}\n"
            f"Total Cost: ${total:.2f}"
        )

    # ======================================================
    # LOGOUT
    # ======================================================

    def logout(self):
        answer = messagebox.askyesno(
            "Logout",
            "Are you sure you want to logout?"
        )

        if answer:
            self.show_login()

    # ======================================================
    # CLEAR WINDOW
    # ======================================================

    def clear_window(self):
        for widget in self.root.winfo_children():
            widget.destroy()


# ==========================================================
# PROGRAM START
# ==========================================================

if __name__ == "__main__":
    root = tk.Tk()

    app = BicycleWorkshopApp(root)

    root.mainloop()

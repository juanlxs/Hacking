# Hacking

Bienvenido a mi repositorio dedicado al hacking ético. Este espacio está diseñado para documentar mi progreso y aprendizaje mientras entreno con diversas plataformas de práctica de seguridad informática, incluyendo HackTheBox, VulnHub y PortSwigger.

## Objetivos del Proyecto

- **Entrenamiento Práctico**: Explorar y resolver diferentes máquinas virtuales y desafíos de seguridad para mejorar mis habilidades de penetración y análisis de vulnerabilidades.

- **Documentación**: Mantener un registro de técnicas, herramientas y enfoques utilizados en cada ejercicio, junto con soluciones detalladas y lecciones aprendidas.

- **Colaboración**: Fomentar un ambiente de aprendizaje colaborativo compartiendo conocimientos y recursos útiles con otros entusiastas de la ciberseguridad.

## VulnHub

[Darkhole I](https://github.com/juanlxs/Hacking/tree/DarkHole-I) 
"""
from tkinter import *
from tkinter import simpledialog
import utils
from threading import Thread

class GuiKiosk:
    def __init__(self, kiosk, root):
        self.kiosk = kiosk
        self.kiosk_obj = utils.kiosks[kiosk["nombre"]]

        # Contenedor del kiosko
        kframe = Frame(root, bg="#0d0d0d")
        kframe.pack(padx=10, pady=5, fill="x")

        # Label de estado
        self.label = Label(kframe, text=kiosk["nombre"],
                           font=("Consolas", 10, "bold"),
                           bg="#0d0d0d", fg="#39ff14", width=18)
        self.label.pack(side=LEFT, padx=5)

        # Estilo común para botones
        btn_opts = {
            "font": ("Consolas", 10),
            "bg": "#111111",
            "fg": "#39ff14",
            "activebackground": "#1a1a1a",
            "activeforeground": "#00ff99",
            "relief": "flat",
            "borderwidth": 1,
            "highlightthickness": 0,
            "padx": 6,
            "pady": 2,
            "width": 8
        }

        smbbutton = Button(kframe, text="SMB", command=self.open_explorer, **btn_opts)
        smbbutton.pack(side=RIGHT, padx=2)

        rdpbutton = Button(kframe, text="RDP", command=self.kiosk_obj.open_rdp, **btn_opts)
        rdpbutton.pack(side=RIGHT, padx=2)

        backupbutton = Button(kframe, text="Backup", command=self.backup_kiosk, **btn_opts)
        backupbutton.pack(side=RIGHT, padx=2)

        pingbutton = Button(kframe, text="Ping", command=self.kiosk_obj.ping, **btn_opts)
        pingbutton.pack(side=RIGHT, padx=2)

        testbutton = Button(kframe, text="Test", command=self.update, **btn_opts)
        testbutton.pack(side=RIGHT, padx=2)

        self.buttons = [smbbutton, rdpbutton, pingbutton]
        self.update()

    def update_z_creds(self):
        if utils.z_creds["user"] == "" or utils.z_creds["pass"] == "":
            user = simpledialog.askstring("User", "Enter user:")
            passw = simpledialog.askstring("Password", "Enter password:", show="*")
            utils.z_creds["user"] = user
            utils.z_creds["pass"] = passw

    def backup_kiosk(self):
        self.update_z_creds()
        self.kiosk_obj.backup()

    def open_explorer(self):
        self.update_z_creds()
        self.kiosk_obj.open_explorer()

    def update(self):
        def _update():
            if self.kiosk_obj.almacenado:
                self.label.config(text="✓ ALMAC", bg="#0d0d0d", fg="#666666")
                return

            status = self.kiosk_obj.test()
            if status == utils.OK:
                self.label.config(text="✔ OK", bg="#0d0d0d", fg="#00ff00")
                for b in self.buttons:
                    b.config(fg="#39ff14")
            elif status == utils.TIMEOUT:
                self.label.config(text="✖ TIMEOUT", bg="#0d0d0d", fg="#ff3333")
                for b in self.buttons:
                    b.config(fg="#ff3333")
            else:
                self.label.config(text="⚠ ERROR", bg="#0d0d0d", fg="#ffaa00")
                for b in self.buttons:
                    b.config(fg="#ffaa00")

        t = Thread(target=_update)
        t.start()
        self.label.after(10000, self.update)


# ========================
# 🧩 Interfaz Principal
# ========================

root = Tk()
root.title("SheepDip Dashboard")
root.configure(bg="#0d0d0d")

# Título visual superior
title = Label(root, text=">> SHEEPDIP OPS DASHBOARD <<",
              font=("Consolas", 12, "bold"),
              bg="#0d0d0d", fg="#39ff14")
title.pack(pady=10)

# Contenedor principal
frame = Frame(root, bg="#0d0d0d")
frame.pack()

# Cargar todos los kioskos
for kiosk in utils.kiosk_data:
    k = GuiKiosk(kiosk, root)

root.mainloop()
"""

import tkinter as tk
from tkinter import messagebox
import spotipy
from spotipy.oauth2 import SpotifyOAuth

# Autenticación con Spotify
def authenticate_spotify():
    sp = spotipy.Spotify(auth_manager=SpotifyOAuth(
        client_id='T4e6c49d6d9f043f9ad878cb8d1fb1b1f',  # Client ID de Spotify
        client_secret='e12c8847596942be99f5938e44b34b42T',  # Client Secret
        redirect_uri='https://developer.spotify.com/documentation/web-api/reference/get-playlist',  # La URI de redirección
        scope='playlist-read-private user-read-private'  # Permisos necesarios
    ))

    print("Autenticación exitosa.")
    return sp

# Listas de reproducción
def get_user_playlists(sp):
    try:
        playlists = sp.current_user_playlists()['items']
        if not playlists:
            messagebox.showinfo("Información", "No se encontraron playlists.")
        return playlists
    except Exception as e:
        messagebox.showerror("Error", f"Hubo un problema al obtener las playlists: {e}")
        return []

# Reproducción en la interfaz gráfica
def display_playlists_ui(playlists):
    # Limpiar cualquier contenido previo en el frame izquierdo
    for widget in frame_left.winfo_children():
        widget.destroy()

    # Título de las listas de reproducción
    playlist_title = tk.Label(frame_left, text="PLAYLISTS", bg="green", fg="black", font=("Helvetica", 14, "bold"))
    playlist_title.pack(fill=tk.X)

    # Mostrar las playlists en botones
    if playlists:
        for i, playlist in enumerate(playlists):
            playlist_name = playlist['name']
            # Crear un botón para cada playlist
            button = tk.Button(frame_left, text=playlist_name, width=20, bg="gray", fg="white", 
                               command=lambda p=playlist_name: open_playlist(p))
            button.pack(pady=2)

def open_playlist(playlist_name):
    messagebox.showinfo("Playlist Seleccionada", f"Has seleccionado la playlist: {playlist_name}")

# Lógica para obtener playlists y mostrarlas
def obtener_playlists(sp):
    playlists = get_user_playlists(sp)
    display_playlists_ui(playlists)

# Interfaz gráfica con Tkinter
def main():
    # Autenticación con Spotify
    sp = authenticate_spotify()

    # Configuración de la ventana principal
    root = tk.Tk()
    root.title("Playlist Interface")
    root.geometry("800x500")
    root.configure(bg="black")

    # Marco izquierdo para la lista de playlists
    global frame_left
    frame_left = tk.Frame(root, bg="black")
    frame_left.pack(side=tk.LEFT, fill=tk.Y)

    # Botón para obtener las playlists desde Spotify
    btn_playlists = tk.Button(root, text="Obtener listas de reproducción", command=lambda: obtener_playlists(sp))
    btn_playlists.pack(pady=10)

    # Iniciar la interfaz gráfica
    root.mainloop()

if __name__ == "__main__":
    main()

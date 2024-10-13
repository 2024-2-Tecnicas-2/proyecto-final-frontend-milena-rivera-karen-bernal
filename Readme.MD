
# Proyecto final - Frontend
## Proyecto final Técnicas de Programación 2

import tkinter as tk
from tkinter import messagebox
import spotipy
from spotipy.oauth2 import SpotifyOAuth

# Autenticación con Spotify
def authenticate_spotify():
    sp = spotipy.Spotify(auth_manager=SpotifyOAuth(client_id="your_client_id",
                                                   client_secret="your_client_secret",
                                                   redirect_uri="http://localhost:8888/callback",
                                                   scope="user-library-read playlist-read-private"))
    print("Autenticación exitosa en la consola.")
    return sp

# Funcionalidad de Búsqueda de Recomendaciones
def get_recommendations(sp, seed_type, seed_input):
    if seed_type == 'artista':
        recommendations = sp.recommendations(seed_artists=[seed_input], limit=10)['tracks']
    elif seed_type == 'género':
        recommendations = sp.recommendations(seed_genres=[seed_input], limit=10)['tracks']
    elif seed_type == 'canción':
        recommendations = sp.recommendations(seed_tracks=[seed_input], limit=10)['tracks']
    else:
        recommendations = []

    print("\nRecomendaciones obtenidas desde la consola:")
    for i, track in enumerate(recommendations):
        print(f"{i+1}. {track['name']} by {track['artists'][0]['name']}")
    return recommendations

# Funcionalidad para mostrar recomendaciones en la interfaz gráfica
def display_recommendations_ui(recommendations):
    rec_window = tk.Toplevel()
    rec_window.title("Recomendaciones")
    for i, track in enumerate(recommendations):
        label = tk.Label(rec_window, text=f"{i+1}. {track['name']} by {track['artists'][0]['name']}")
        label.pack()

# Obtención de Listas de Reproducción
def get_user_playlists(sp):
    playlists = sp.current_user_playlists()['items']
    
    print("\nListas de reproducción obtenidas desde la consola:")
    for i, playlist in enumerate(playlists):
        print(f"{i+1}. {playlist['name']} (Total Tracks: {playlist['tracks']['total']})")
    return playlists

# Funcionalidad para mostrar listas de reproducción en la interfaz gráfica
def display_playlists_ui(playlists):
    playlists_window = tk.Toplevel()
    playlists_window.title("Listas de Reproducción")
    for i, playlist in enumerate(playlists):
        label = tk.Label(playlists_window, text=f"{i+1}. {playlist['name']} (Total Tracks: {playlist['tracks']['total']})")
        label.pack()

# Lógica para los botones de la interfaz
def buscar_recomendaciones(sp):
    seed_type = entry_seed_type.get().lower()
    seed_input = entry_seed_input.get()
    
    if not seed_input:
        messagebox.showwarning("Advertencia", "Introduce un valor para la búsqueda.")
        return
    
    recommendations = get_recommendations(sp, seed_type, seed_input)
    display_recommendations_ui(recommendations)

def obtener_playlists(sp):
    playlists = get_user_playlists(sp)
    display_playlists_ui(playlists)

# Interfaz Gráfica con Tkinter
def main():
    sp = authenticate_spotify()

    root = tk.Tk()
    root.title("MusicPy - Híbrido")

    label_seed_type = tk.Label(root, text="Buscar por (artista/género/canción):")
    label_seed_type.pack(pady=5)

    global entry_seed_type
    entry_seed_type = tk.Entry(root)
    entry_seed_type.pack(pady=5)

    label_seed_input = tk.Label(root, text="Introduce el ID o nombre:")
    label_seed_input.pack(pady=5)

    global entry_seed_input
    entry_seed_input = tk.Entry(root)
    entry_seed_input.pack(pady=5)

    btn_recommendations = tk.Button(root, text="Buscar recomendaciones", command=lambda: buscar_recomendaciones(sp))
    btn_recommendations.pack(pady=10)

    btn_playlists = tk.Button(root, text="Obtener listas de reproducción", command=lambda: obtener_playlists(sp))
    btn_playlists.pack(pady=10)

    btn_exit = tk.Button(root, text="Salir", command=root.quit)
    btn_exit.pack(pady=10)

    root.mainloop()

if __name__ == "__main__":
    main()
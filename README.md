# Signature_Verifier

import tkinter as tk
from tkinter.filedialog import askopenfilename
from tkinter import messagebox, PhotoImage
import cv2
from numpy import result_type
from skimage.metrics import structural_similarity as ssim
from PIL import ImageTk, Image

THRESHOLD = 85

def browsefunc(ent):
    filename = askopenfilename(filetypes=([
        ("image", ".jpeg"),
        ("image", ".png"),
        ("image", ".jpg"),
    ]))
    ent.delete(0, tk.END)
    ent.insert(tk.END, filename)

def match(path1, path2):
    img1 = cv2.imread(path1)
    img2 = cv2.imread(path2)

    img1 = cv2.cvtColor(img1, cv2.COLOR_BGR2GRAY)
    img2 = cv2.cvtColor(img2, cv2.COLOR_BGR2GRAY)

    img1 = cv2.resize(img1, (300, 300))
    img2 = cv2.resize(img2, (300, 300))

    cv2.waitKey(0)
    cv2.destroyAllWindows()
    similarity_value = "{:.2f}".format(ssim(img1, img2)*100)
    return float(similarity_value)

def checkSimilarity(window, path1, path2):
    result = match(path1=path1, path2=path2)
    if(result <= THRESHOLD):
        messagebox.showerror("False: Signatures Do Not Match \u274c",
                             "Signatures are "+str(result)+f" % similar!!")
        pass
    else:
        messagebox.showinfo("True: Signatures Match \N{check mark}",
                            "Signatures are "+str(result)+f" % similar!!")
    return True


root = tk.Tk()
root.title("Signature Verifier")
root.geometry("500x400")
icon = PhotoImage(file = 'icon.png')
root.iconphoto(False, icon)
bg = ImageTk.PhotoImage(Image.open("bg1.jpg"))
bg_label = tk.Label( root, image = bg)
bg_label.pack()
uname_label = tk.Label(root, text="Please select the two signature files:", font=10,bg="#cfdee5")
uname_label.place(x=90, y=50)

img1_message = tk.Label(root, text="Signature 1", font=10,bg="#cfdee5")
img1_message.place(x=10, y=120)

image1_path_entry = tk.Entry(root, font=10)
image1_path_entry.place(x=150, y=120)

img1_browse_button = tk.Button(
    root, text="Browse",bg="#cee4e9", font=10, command=lambda: browsefunc(ent=image1_path_entry))
img1_browse_button.place(x=400, y=115)

image2_path_entry = tk.Entry(root, font=10)
image2_path_entry.place(x=150, y=240)

img2_message = tk.Label(root, text="Signature 2", font=10,bg="#cfdee5")
img2_message.place(x=10, y=240)

img2_browse_button = tk.Button(
    root, text="Browse", font=10,bg="#cee4e9", command=lambda: browsefunc(ent=image2_path_entry))
img2_browse_button.place(x=400, y=235)

compare_button = tk.Button(
    root, text="Compare",bg="#cee4e9", font=10, command=lambda: checkSimilarity(window=root,
                                                                   path1=image1_path_entry.get(),
                                                                   path2=image2_path_entry.get()))

compare_button.place(x=200, y=320)
root.mainloop()

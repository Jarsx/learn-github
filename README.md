import os
import tkinter as tk
from tkinter import *
from tkinter import filedialog
from tkinter import messagebox

class TextEditor():
    def __init__(self):
        self.mypro = Tk()
        self.mypro.title("Text Editor")
        self.mypro.geometry("800x600")
        self.display()
    
    def open_files(self):
        file_type=[("all files",".*")]
        self.file=filedialog.askopenfilename(parent=self.mypro,initialdir=os.getcwd(),title="Please select a folder:",filetypes=file_type)
        if self.file[-4:]==".txt":
            fout=open(self.file,'r')
            self.content=fout.read()
            self.edit_text.delete('1.0', 'end')
            self.edit_text.insert('1.0',self.content)
            self.count_on_file()
        else:
            messagebox.showerror("Error","Failed to open : invalid file type")
    
    def display(self):
        self.edit_text=tk.Text(self.mypro,bg="black",fg="white")
        self.edit_text.place(width=620,height=500,rely=0.03,relx=0.2)

        open_bt = Button(self.mypro, text='Open', fg='black', bg='#0000ff',command=self.open_files)
        open_bt.place(width=120, rely=0.03, relx=0.03)

        save_bt = Button(self.mypro, text="Save", fg='black', bg='#00ff00',command=self.save_files)
        save_bt.place(width=120, rely=0.09, relx=0.03)

        clear_bt = Button(self.mypro, text="Clear", fg='black', bg='#ff0000',command=self.clear_files)
        clear_bt.place(width=120, rely=0.15, relx=0.03)

        search_bt = Button(self.mypro, text="Search", fg='black', bg='#ffff00',command=self.search_files)
        search_bt.place(width=120, rely=0.26, relx=0.03)

        self.text_search=StringVar()
        self.search_entry = Entry(self.mypro,textvariable=self.text_search)
        self.search_entry.place(width=120, rely=0.21, relx=0.03)

        self.show_word=Label(self.mypro,text=f"Words: 0, Characters(no spaces): 0")
        self.show_word.place(relx=0.03,rely=0.9)
        
        self.show_size=Label(self.mypro,text=f"Flie Size: N/A")
        self.show_size.place(relx=0.03,rely=0.95)
        
        self.edit_text.bind('<KeyRelease>', self.count_realtime)

    def save_files(self):
        fout=open(self.file,'w')
        self.content = self.edit_text.get('1.0', 'end-1c')
        fout.write(self.content)
        

    def clear_files(self):
        self.edit_text.delete('1.0', 'end')
    
    def count_on_file(self):
        self.count_bytes=0
        for i in self.content:
            self.count_bytes+=1
        self.char=self.content.split()
        self.char="".join(self.char)
            
        self.count_word=0
        self.word=self.content.split()
        self.show_word=Label(self.mypro,text=f"Words: {len(self.word)}, Characters(no spaces): {len(self.char)}")
        self.show_word.place(relx=0.03,rely=0.9)
        self.show_word.config(text=f"Words: {len(self.word)}, Characters (no spaces): {len(self.char)}")
        self.show_size.config(text=f"File Size: {self.count_bytes} bytes") 

    def count_realtime(self,event):
            self.content=self.edit_text.get("1.0", tk.END)
            self.count_bytes=0
            for i in self.content:
                self.count_bytes+=1   
            
            self.char=self.content.split()
            self.char="".join(self.char)
            
            self.count_word=0
            self.word=self.content.split()               

            self.show_word=Label(self.mypro,text=f"Words: {len(self.word)}, Characters(no spaces): {len(self.char)}")
            self.show_word.place(relx=0.03,rely=0.9)
            self.show_word.config(text=f"Words: {len(self.word)}, Characters (no spaces): {len(self.char)}")
            self.show_size.config(text=f"File Size: {self.count_bytes} bytes")

    def search_files(self):
        self.pattern = self.search_entry.get()
        if not(self.pattern in self.content):
            messagebox.showinfo("Search", "Search term not found.")
        self.edit_text.tag_remove("highlight", "1.0", "end")
        self.highlight_word()

    def highlight_word(self):
        start_pos = "1.0"

        while True:
            start_pos = self.edit_text.search(self.pattern, start_pos, stopindex="end", nocase=True)
            if not start_pos:
                break
            end_pos = f"{start_pos}+{len(self.pattern)}c"
            self.edit_text.tag_add("highlight", start_pos, end_pos)
            start_pos = end_pos
            found = True

        self.edit_text.tag_config("highlight", background="yellow", foreground="black")
        if found:
            start_pos = self.edit_text.search(self.pattern, "1.0", stopindex="end", nocase=True)
            self.edit_text.see(start_pos)
            self.edit_text.tag_add("highlight", start_pos, f"{start_pos}+{len(self.pattern)}c")
            self.edit_text.focus_set()
        
program = TextEditor()
program.mypro.mainloop()

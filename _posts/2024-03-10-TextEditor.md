---
title: 紫藤文本编辑器
data: 2024-03-16 17:55:11 +0800
image_path: "/assets/img/"
categories: [Python, 程序]
tags: [Python , 文章 , 编辑 , 程序]
---

写了一个适用于Jekyll文章或普通文本文件的文本编辑器，可以自动读取Jekyll格式的文章，并自动填充时间、文件名、分类、标签、图片等信息格式。

**特性**:

- 读取/创建Jekyll格式的文章
  - 自动填充时间
  - 自动格式化文件名
  - 可视化头文件填写
  - 自动创建在`_post`中
  - 分类数量限制提示
  - 必要信息填充提示
- 读取/创建其他文本文件

**注意事项**

- 若使用文章编辑器请将程序放在`_post`同级目录下
  - 如果没有则会自动创建目录

**获取**

[蓝奏云](https://dachuziyi.lanzouq.com/iTDi31rlxqaj)

具体源代码如下:

```python
# 模块安装检测
import importlib.util
import subprocess
import sys

print("正在启动中，可能会需要安装模块\n若模块安装长时间没反应请检查网络连接或重新启动软件。\n模块安装使用清华大学镜像源\n---====================---")

def install_module(module_name):
    subprocess.check_call([sys.executable, "-m", "pip", "install", "--index-url", "https://pypi.tuna.tsinghua.edu.cn/simple", module_name])


def check_and_install(module_name):
    spec = importlib.util.find_spec(module_name)
    if spec is None:
        print(f"模块 {module_name} 好像还没装,正在帮你装上,等一会昂...")
        install_module(module_name)
        print(f"模块 {module_name} 装好啦~")


# 需要检查的模块列表
required_modules = ["tkinter", "configparser", "ttkbootstrap", "pyyaml", "pillow"]
for module in required_modules:
    check_and_install(module)

print("所需模块已全部具备，正在打开GUI")

# 模块导入
import tkinter as tk
from tkinter import messagebox, filedialog
import webbrowser
import ttkbootstrap as ttk
from ttkbootstrap.constants import *
import os
from datetime import datetime
import yaml
from PIL import Image, ImageTk

# 程序所在目录
current_directory = os.path.dirname(os.path.abspath(__file__))


# 定义主窗口类
class MainApplication(tk.Tk):
    def __init__(self):
        super().__init__()

        self.title("紫藤文本编辑器-WisteriaTextEditor")
        icon = Image.open(current_directory + "/img/favicon.ico")
        icon_photo = ImageTk.PhotoImage(icon)
        self.iconphoto(True, icon_photo)
        welcome_label = ttk.Label(self, text="欢迎使用紫藤文本编辑器", bootstyle="info")
        welcome_label.grid(row=0, column=1, padx=5, pady=5, sticky="w")
        self.editor_button = ttk.Button(
            self, text="创建/打开Jekyll文章", command=self.open_post_editor
        )
        self.editor_button.grid(row=1, column=0, padx=5, pady=5, sticky="w")
        editor_label = ttk.Label(
            self,
            text="适用于Jekyll文章的编辑器\n默认导出到当前目录下_post中",
            bootstyle="warning",
        )
        editor_label.grid(row=1, column=1, padx=5, pady=5, sticky="w")

        self.txt_button = ttk.Button(
            self, text="创建/打开文本文件", command=self.open_txt_editor
        )
        self.txt_button.grid(row=2, column=0, padx=5, pady=5, sticky="w")
        editor_label = ttk.Label(
            self,
            text="适用于一般TXT、Markdown\n或其他文本文件的编辑器",
            bootstyle="warning",
        )
        editor_label.grid(row=2, column=1, padx=5, pady=5, sticky="w")

        self.about_button = ttk.Button(
            self, text="关于", command=self.about, bootstyle="warning"
        )
        self.about_button.grid(row=5, column=0, padx=5, pady=5, sticky="w")

        # 打开官网
        self.open_website_button = ttk.Button(
            self,
            text="打开官网（应该算是）",
            command=lambda: webbrowser.open(
                "https://dachuziyi.github.io/Notebook/posts/TextEditor/"
            ),
            bootstyle="info",
        )
        self.open_website_button.grid(row=5, column=1, padx=5, pady=5, sticky="w")
        self.minsize(500, 500)

        self.columnconfigure(1, weight=1)

    def about(self):
        messagebox.showinfo(
            "关于",
            "紫藤文本编辑器\n版本：1.0.0\n作者：大触紫衣WisteriaZy\n联系方式：dczyxnfs@qq.com\n\n基于Python3.12.2开发，使用Tkinter、ttkbootstrap、PyYAML、Pillow等模块。",
        )

    def open_post_editor(self):
        # 隐藏主窗口
        self.withdraw()

        # 创建并显示新窗口
        window_postEditor = PostEditor()
        window_postEditor.grab_set()  # 确保新窗口在最前面
        window_postEditor.wait_window()  # 等待新窗口关闭

        # 新窗口关闭后，重新显示主窗口
        self.deiconify()

    def open_txt_editor(self):
        # 隐藏主窗口
        self.withdraw()

        # 创建并显示新窗口
        window_txtEditor = TxtEditor()
        window_txtEditor.grab_set()  # 确保新窗口在最前面
        window_txtEditor.wait_window()  # 等待新窗口关闭

        # 新窗口关闭后，重新显示主窗口
        self.deiconify()


# 编辑器窗口
class PostEditor(tk.Toplevel):
    def __init__(self):
        super().__init__()
        self.title("Markdown文章编辑")
        self.minsize(500, 500)

        # 文本定义
        tip_content_text = "   在此处输入内容\n   注意使用markdown语法"
        tip_title_entry = "     显示在文章中的标题"
        tip_author_entry = "     不填则为默认"
        tip_image_entry = "     （仅须填文件名）将显示在标题上，path为上一项"
        tip_img_alt_entry = "      上一项图片的描述"
        tip_categories_text = "   最多两项\n   换行分隔"
        tip_tags_text = "   不限制数量\n   换行分隔"

        # 文本框提示
        def content_text_on_content_text_focusin(event):
            if content_text.get("1.0", "end-1c") == tip_content_text:
                content_text.delete("1.0", "end")

        def author_entry_on_content_text_focusin(event):
            if author_entry.get() == tip_author_entry:
                author_entry.delete(0, "end")

        def image_entry_on_content_text_focusin(event):
            if image_entry.get() == tip_image_entry:
                image_entry.delete(0, "end")

        def img_alt_entry_on_content_text_focusin(event):
            if img_alt_entry.get() == tip_img_alt_entry:
                img_alt_entry.delete(0, "end")

        def categories_text_on_content_text_focusin(event):
            if categories_text.get("1.0", "end-1c") == tip_categories_text:
                categories_text.delete("1.0", "end")

        def tags_text_on_content_text_focusin(event):
            if tags_text.get("1.0", "end-1c") == tip_tags_text:
                tags_text.delete("1.0", "end")

        def title_entry_on_content_text_focusin(event):
            if title_entry.get() == tip_title_entry:
                title_entry.delete(0, "end")

        # 失去文本框提示
        def content_text_on_content_text_focusout(event):
            if not content_text.get("1.0", "end-1c"):
                content_text.delete("1.0", "end")
                content_text.insert("1.0", tip_content_text)

        def author_entry_on_content_text_focusout(event):
            if not author_entry.get():
                author_entry.delete(0, "end")
                author_entry.insert(0, tip_author_entry)

        def image_entry_on_content_text_focusout(event):
            if not image_entry.get():
                image_entry.delete(0, "end")
                image_entry.insert(0, tip_image_entry)

        def img_alt_entry_on_content_text_focusout(event):
            if not img_alt_entry.get():
                img_alt_entry.delete(0, "end")
                img_alt_entry.insert(0, tip_img_alt_entry)

        def categories_text_on_content_text_focusout(event):
            if not categories_text.get("1.0", "end-1c"):
                categories_text.delete("1.0", "end")
                categories_text.insert("1.0", tip_categories_text)

        def tags_text_on_content_text_focusout(event):
            if not tags_text.get("1.0", "end-1c"):
                tags_text.delete("1.0", "end")
                tags_text.insert("1.0", tip_tags_text)

        def title_entry_on_content_text_focusout(event):
            if not title_entry.get():
                title_entry.delete(0, "end")
                title_entry.insert(0, tip_title_entry)

        # 打开文件
        def open_markdown():
            # 打开文件对话框并获取选择的文件路径
            file_path = filedialog.askopenfilename(filetypes=[("Markdown文件", "*.md")])

            if file_path:
                # 读取文件内容
                with open(file_path, "r", encoding="utf-8") as file:
                    content = file.read()

                # 解析文件内容
                lines = content.split("\n")
                (
                    author,
                    title,
                    content,
                    categories,
                    tags,
                    image_path,
                    alt,
                    pin_status,
                ) = (
                    "",
                    "",
                    "",
                    [],
                    [],
                    "",
                    "",
                    True,
                )
                in_metadata = False
                for line in lines:
                    line = line.strip()
                    if line.startswith("---"):
                        in_metadata = not in_metadata
                        continue
                    if in_metadata:
                        if line.startswith("author:"):
                            author = line.split(":")[1].strip()
                        elif line.startswith("title:"):
                            title = line.split(":")[1].strip()
                        elif line.startswith("image_path:"):
                            image_path = line.split(":")[1].strip().strip('"')
                        elif line.startswith("image:"):
                            image_info = line.split(":")[1]
                            if image_info:
                                image_info_dict = yaml.safe_load(image_info)
                                if image_info_dict and "path" in image_info_dict:
                                    image_path = image_info_dict["path"].strip('"')
                                    if "alt" in image_info_dict:
                                        alt = image_info_dict["alt"].strip('"')
                        elif line.startswith("pin:"):
                            pin_status = line.split(":")[1].lower() == "true"
                        elif line.startswith("categories:"):
                            categories_str = line.split(":")[1].strip().strip("[]")
                            categories = [
                                cat.strip() for cat in categories_str.split(",")
                            ]
                        elif line.startswith("tags:"):
                            tags_str = line.split(":")[1].strip().strip("[]")
                            tags = [tag.strip() for tag in tags_str.split(",")]

                    else:
                        content += line + "\n"

                # 检查输入框是否已经包含内容，如果有内容则询问用户是否覆盖
                confirm = messagebox.askyesno("打开文件", "确认打开？\n" + file_path)
                if not confirm:
                    return

                # 删去多余的换行符
                while content.startswith("\n"):
                    content = content[1:]
                # 将解析后的内容填充到对应的输入框中
                author_entry.delete(0, tk.END)
                author_entry.insert(0, author)

                title_entry.delete(0, tk.END)
                title_entry.insert(0, title)

                content_text.delete("1.0", tk.END)
                content_text.insert("1.0", content)

                categories_text.delete("1.0", tk.END)
                categories_text.insert("1.0", "\n".join(categories))

                tags_text.delete("1.0", tk.END)
                tags_text.insert("1.0", "\n".join(tags))

                # 处理图片相关字段（假设您有对应的文本框或变量）
                img_path_entry.delete(0, tk.END)
                img_path_entry.insert(0, image_path)

                img_alt_entry.delete(0, tk.END)
                img_alt_entry.insert(0, alt)

                # 创建一个 IntVar 对象来表示是否置顶，并将其值设置为解析得到的 pin 状态
                option_pin = tk.IntVar(value=int(pin_status))

        def save_markdown():
            # 获取输入框中的内容
            author = author_entry.get()
            title = title_entry.get()
            content = content_text.get("1.0", "end-1c")
            selected_option_pin = option_pin.get()
            image_path = img_path_entry.get()
            image = image_entry.get()
            alt = img_alt_entry.get()

            if image != tip_image_entry:
                if alt != tip_img_alt_entry:
                    imageINFO = (
                        'image:\n  path: "'
                        + image_path
                        + '"\n  '
                        + 'alt: "'
                        + alt
                        + '"\n'
                    )
                else:
                    imageINFO = 'image:\n  path: "' + image_path + '"\n'
            else:
                imageINFO = ""

            if image_path != "":
                image_pathINFO = 'image_path: "' + image_path + '"\n'
            else:
                image_pathINFO = ""

            if selected_option_pin == 0:
                pinINFO = "pin: true\n"
            else:
                pinINFO = ""

            if title != tip_title_entry:
                authorINFO = ""
            else:
                authorINFO = "author: " + author + "\n"

            if title != tip_title_entry and not title.isspace():
                # 替换标题中的空格为短横线
                title = title.replace(" ", "-")
                categories = categories_text.get("1.0", "end-1c").strip()
                tags = tags_text.get("1.0", "end-1c").strip()

                if categories != tip_categories_text and not categories.isspace():
                    if len(categories.split("\n")) <= 2:
                        categoriesINFO = (
                            "categories: [" + ", ".join(categories.split("\n")) + "]\n"
                        )
                    else:
                        messagebox.showerror("警告", "分类应不超过2个")
                        return
                else:
                    categoriesINFO = ""

                if tags != tip_tags_text and not tags.isspace():
                    tagsINFO = "tags: [" + ", ".join(tags.split("\n")) + "]\n"
                else:
                    tagsINFO = ""

                # 获取当前系统时间并格式化
                current_time = datetime.now().strftime("%Y-%m-%d")
                current_timeForMD = datetime.now().strftime("%Y-%m-%d %H:%M:%S +0800")

                # 拼接Markdown格式的内容
                markdown_content = f"---\n{authorINFO}title: {title}\ndata: {current_timeForMD}\n{image_pathINFO}{categoriesINFO}{tagsINFO}{pinINFO}{imageINFO}---\n\n{content}"

                # 保存为Markdown文件
                filename = f"{current_time}-{title}.md"

                # 获取当前脚本所在目录的上级目录作为目标目录
                script_dir = os.path.dirname(os.path.abspath(__file__))
                target_dir = os.path.join(script_dir, "..", "_posts")

                # 确保目标目录存在
                if not os.path.exists(target_dir):
                    os.makedirs(target_dir)

                # 构建完整的输出文件路径
                filename = os.path.join(target_dir, f"{current_time}-{title}.md")

                # 写入文件
                with open(filename, "w", encoding="utf-8") as file:
                    file.write(markdown_content)

                messagebox.showinfo("提示", f"Markdown文档 '{filename}' 已保存成功！")
            else:
                messagebox.showerror("警告", "缺少标题")

        # self.title("紫藤文本编辑器-WisteriaTextEditor")
        # self.minsize(500, 500)

        # 输入框、按钮和文本框
        open_button = ttk.Button(self, text="打开文件", command=open_markdown)
        open_button.grid(row=9, column=0, padx=5, pady=5, sticky="w")

        author_label = ttk.Label(self, text="作者:", bootstyle="info")
        author_label.grid(row=0, column=0, padx=5, pady=5, sticky="w")
        author_entry = ttk.Entry(self, bootstyle="primary")
        author_entry.insert(0, tip_author_entry)
        author_entry.bind("<FocusIn>", author_entry_on_content_text_focusin)
        author_entry.bind("<FocusOut>", author_entry_on_content_text_focusout)
        author_entry.grid(row=0, column=1, padx=5, pady=5, sticky="we")

        # 创建一个 IntVar 对象来跟踪用户的选择
        option_pin = ttk.IntVar()

        # 创建一个 Label 显示“是否置顶：”
        IFpin_label = ttk.Label(self, text="是否置顶:", bootstyle="info")
        IFpin_label.grid(row=1, column=0, padx=5, pady=5, sticky="w")
        # 定义一个 Frame 来容纳并排的单选按钮，并设置其样式
        options_frame = ttk.Frame(self)
        options_frame.grid(row=1, column=1, columnspan=2, padx=5, pady=5, sticky="w")
        # 在 options_frame 中创建“是”和“否”单选按钮
        y_pin = ttk.Radiobutton(options_frame, text="是", variable=option_pin, value=0)
        n_pin = ttk.Radiobutton(options_frame, text="否", variable=option_pin, value=1)
        # 将两个单选按钮水平居中放置在 options_frame 内
        y_pin.pack(side=ttk.LEFT, padx=(0, 10))
        n_pin.pack(side=ttk.LEFT)
        # 初始化 option_pin 的值
        option_pin.set(1)

        img_path_label = ttk.Label(self, text="图片路径:", bootstyle="info")
        img_path_label.grid(row=2, column=0, padx=5, pady=5, sticky="w")
        img_path_entry = ttk.Entry(self, bootstyle="primary")
        img_path_entry.grid(row=2, column=1, padx=5, pady=5, sticky="we")
        img_path_entry.insert(0, "/assets/img/")

        image_label = ttk.Label(self, text="封面图:", bootstyle="info")
        image_label.grid(row=3, column=0, padx=5, pady=5, sticky="w")
        image_entry = ttk.Entry(self, bootstyle="primary")
        image_entry.insert(0, tip_image_entry)
        image_entry.bind("<FocusIn>", image_entry_on_content_text_focusin)
        image_entry.bind("<FocusOut>", image_entry_on_content_text_focusout)
        image_entry.grid(row=3, column=1, padx=5, pady=5, sticky="we")

        img_alt_label = ttk.Label(self, text="描述:", bootstyle="info")
        img_alt_label.grid(row=4, column=0, padx=5, pady=5, sticky="w")
        img_alt_entry = ttk.Entry(self, bootstyle="primary")
        img_alt_entry.insert(0, tip_img_alt_entry)
        img_alt_entry.bind("<FocusIn>", img_alt_entry_on_content_text_focusin)
        img_alt_entry.bind("<FocusOut>", img_alt_entry_on_content_text_focusout)
        img_alt_entry.grid(row=4, column=1, padx=5, pady=5, sticky="we")

        categories_label = ttk.Label(self, text="分类:", bootstyle="inverse-primary")
        categories_label.grid(row=5, column=0, padx=5, pady=5, sticky="w")
        categories_text = ttk.Text(self, height=2, width=50)
        categories_text.insert("1.0", tip_categories_text)
        categories_text.bind("<FocusIn>", categories_text_on_content_text_focusin)
        categories_text.bind("<FocusOut>", categories_text_on_content_text_focusout)
        categories_text.grid(row=5, column=1, padx=5, pady=5, sticky="we")

        tags_label = ttk.Label(self, text="标签:", bootstyle="inverse-primary")
        tags_label.grid(row=6, column=0, padx=5, pady=5, sticky="w")
        tags_text = ttk.Text(self, height=3, width=50)
        tags_text.insert("1.0", tip_tags_text)
        tags_text.bind("<FocusIn>", tags_text_on_content_text_focusin)
        tags_text.bind("<FocusOut>", tags_text_on_content_text_focusout)
        tags_text.grid(row=6, column=1, padx=5, pady=5, sticky="we")

        title_label = ttk.Label(self, text="标题:", bootstyle="inverse-warning")
        title_label.grid(row=7, column=0, padx=5, pady=5, sticky="w")
        title_entry = ttk.Entry(self, bootstyle="info")
        title_entry.insert(0, tip_title_entry)
        # title_entry.config(fg="gray")
        title_entry.bind("<FocusIn>", title_entry_on_content_text_focusin)
        title_entry.bind("<FocusOut>", title_entry_on_content_text_focusout)
        title_entry.grid(row=7, column=1, padx=5, pady=5, sticky="we")

        content_label = ttk.Label(self, text="内容:", bootstyle="inverse-warning")
        content_label.grid(row=8, column=0, padx=5, pady=5, sticky="nw")
        content_text = ttk.Text(self, height=10, width=30)
        content_text.insert("1.0", tip_content_text)
        # 当获取焦点时触发事件处理函数
        content_text.bind("<FocusIn>", content_text_on_content_text_focusin)
        # 当失去焦点时触发事件处理函数
        content_text.bind("<FocusOut>", content_text_on_content_text_focusout)
        content_text.grid(row=8, column=1, padx=5, pady=5, sticky="nsew")

        save_button = ttk.Button(
            self, text="保存为Markdown", command=save_markdown, bootstyle="success"
        )
        save_button.grid(row=9, column=1, padx=5, pady=5, sticky="we")

        # 设置网格布局权重
        self.columnconfigure(1, weight=1)
        self.rowconfigure(8, weight=1)


class TxtEditor(tk.Toplevel):
    def __init__(self):
        super().__init__()
        self.title("文本文件编辑")
        self.minsize(500, 500)

        tip_content_text = "在此处输入内容"

        def content_text_on_content_text_focusin(event):
            if content_text.get("1.0", "end-1c") == tip_content_text:
                content_text.delete("1.0", "end")

        def content_text_on_content_text_focusout(event):
            if not content_text.get("1.0", "end-1c"):
                content_text.delete("1.0", "end")
                content_text.insert("1.0", tip_content_text)

        def open_txt():
            # 打开文件选择对话框，选择txt，md，或者其他文件
            file_path = filedialog.askopenfilename(
                filetypes=[
                    ("文本文件", "*.txt"),
                    ("Markdown文件", "*.md"),
                    ("所有文件", "*.*"),
                ]
            )

            if file_path:
                # 读取文件内容
                with open(file_path, "r", encoding="utf-8") as file:
                    content = file.read()

                content_text.delete("1.0", tk.END)
                content_text.insert("1.0", content)

                # 检查输入框是否已经包含内容，如果有内容则询问用户是否覆盖
                confirm = messagebox.askyesno("打开文件", "确认打开？\n" + file_path)
                if not confirm:
                    return

        def save_txt():
            # 保存为txt文件
            filename = filedialog.asksaveasfilename(
                filetypes=[("文本文件", "*.txt"), ("Markdown文件", "*.md")]
            )
            if filename:
                with open(filename, "w", encoding="utf-8") as file:
                    file.write(content_text.get("1.0", tk.END))
                messagebox.showinfo("提示", f"文本文件 '{filename}' 已保存成功！")

        open_button = ttk.Button(self, text="打开文件", command=open_txt)
        open_button.grid(row=9, column=0, padx=5, pady=5, sticky="w")

        content_label = ttk.Label(self, text="内容:", bootstyle="inverse-warning")
        content_label.grid(row=8, column=0, padx=5, pady=5, sticky="nw")
        content_text = ttk.Text(self, height=10, width=30)
        content_text.insert("1.0", tip_content_text)
        # 当获取焦点时触发事件处理函数
        content_text.bind("<FocusIn>", content_text_on_content_text_focusin)
        # 当失去焦点时触发事件处理函数
        content_text.bind("<FocusOut>", content_text_on_content_text_focusout)
        content_text.grid(row=8, column=1, padx=5, pady=5, sticky="nsew")

        save_button = ttk.Button(
            self, text="保存为Txt", command=save_txt, bootstyle="success"
        )
        save_button.grid(row=9, column=1, padx=5, pady=5, sticky="we")

        # 设置网格布局权重
        self.columnconfigure(1, weight=1)
        self.rowconfigure(8, weight=1)


# 创建启动窗口并运行
if __name__ == "__main__":
    main_app = MainApplication()
    main_app.mainloop()
```

# chat
index.html

```

<!DOCTYPE html>
<html>
<head>
    <title>Chat</title>
</head>
<body>
    <h1>chat</h1>
    <h2>absolutely annoymous</h2>
    <a href="{{ url_for('new_post') }}">New Post</a>
    <hr>
    {% for post in posts %}
    <h3>{{ post[1] }}</h3>
    <p>{{ post[2] }}</p>
    <hr>
    {% endfor %}
</body>
</html>

```

new_post.html

```

<!DOCTYPE html>
<html>
<head>
    <title>New Post</title>
</head>
<body>
    <h1>New Post</h1>
    <form method="POST" action="{{ url_for('new_post') }}">
        <label for="title">Title:</label><br>
        <input type="text" id="title" name="title" required><br><br>
        <label for="content">Content:</label><br>
        <textarea id="content" name="content" required></textarea><br><br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>

```

main.py

```

app = Flask(__name__)
DATABASE = 'bbs.db'

def create_table():
    with sqlite3.connect(DATABASE) as conn:
        cursor = conn.cursor()
        cursor.execute('''CREATE TABLE IF NOT EXISTS posts (
                          id INTEGER PRIMARY KEY AUTOINCREMENT,
                          title TEXT NOT NULL,
                          content TEXT NOT NULL
                          )''')
        conn.commit()

def get_posts():
    with sqlite3.connect(DATABASE) as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM posts ORDER BY id DESC")
        posts = cursor.fetchall()
    return posts

def add_post(title, content):
    with sqlite3.connect(DATABASE) as conn:
        cursor = conn.cursor()
        cursor.execute("INSERT INTO posts (title, content) VALUES (?, ?)", (title, content))
        conn.commit()

@app.route('/')
def index():
    posts = get_posts()
    return render_template('index.html', posts=posts)

@app.route('/new_post', methods=['GET', 'POST'])
def new_post():
    if request.method == 'POST':
        title = request.form['title']
        content = request.form['content']
        add_post(title, content)
        return redirect(url_for('index'))
    return render_template('new_post.html')

if __name__ == '__main__':
    create_table()
    app.run(debug=True)

```

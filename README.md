# MWAD-EX_03-To-Do-List-using-JavaScript
## Date: 07-10-2025

## AIM
To create a To-do Application with all features using JavaScript.

## ALGORITHM
### STEP 1
Build the HTML structure (index.html).

### STEP 2
Style the App (style.css).

### STEP 3
Plan the features the To-Do App should have.

### STEP 4
Create a To-do application using Javascript.

### STEP 5
Add functionalities.

### STEP 6
Test the App.

### STEP 7
Open the HTML file in a browser to check layout and functionality.

### STEP 8
Fix styling issues and refine content placement.

### STEP 9
Deploy the website.

### STEP 10
Upload to GitHub Pages for free hosting.

## PROGRAM
```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Simple To‑Do App — JavaScript</title>
  <style>
    :root{
      --bg:#0f1724; --card:#0b1220; --muted:#94a3b8; --accent:#7c3aed; --success:#10b981; --danger:#ef4444;
      --glass: rgba(255,255,255,0.04);
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; background:linear-gradient(180deg,#071025 0%, #071129 50%); color:#e6eef8; display:flex; align-items:center; justify-content:center; padding:28px;
    }
    .wrap{width:100%; max-width:820px}
    header{display:flex;align-items:center;justify-content:space-between;margin-bottom:18px}
    h1{font-size:22px;margin:0}
    p.lead{margin:4px 0 0;color:var(--muted);font-size:13px}

    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); border-radius:12px;padding:18px; box-shadow:0 6px 30px rgba(2,6,23,0.6)}

    .new-task{display:flex;gap:10px}
    .new-task input[type="text"]{flex:1;padding:12px 14px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);background:transparent;color:inherit;font-size:15px}
    .btn{padding:10px 14px;border-radius:10px;border:0;background:var(--accent);color:white;cursor:pointer;font-weight:600}
    .btn.secondary{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted);font-weight:600}
    .controls{display:flex;gap:8px;align-items:center;margin-top:14px;flex-wrap:wrap}

    .list{margin-top:18px}
    .task{display:flex;align-items:center;gap:12px;padding:12px;border-radius:10px;margin-bottom:10px;background:var(--glass);border:1px solid rgba(255,255,255,0.02)}
    .task .left{display:flex;align-items:center;gap:12px;flex:1}
    .checkbox{width:18px;height:18px;border-radius:6px;border:2px solid rgba(255,255,255,0.12);display:inline-flex;align-items:center;justify-content:center;cursor:pointer}
    .checkbox.checked{background:var(--success);border-color:transparent}
    .task .text{font-size:15px}
    .task .text.completed{opacity:0.6;text-decoration:line-through}
    .actions{display:flex;gap:8px}
    .icon-btn{background:transparent;border:0;padding:6px;border-radius:8px;cursor:pointer;color:var(--muted)}
    .icon-btn:hover{color:white}

    .filters{display:flex;gap:8px}
    .filters button{padding:8px 10px;border-radius:8px;border:0;background:transparent;color:var(--muted);cursor:pointer}
    .filters button.active{background:rgba(255,255,255,0.04);color:white}

    .meta{display:flex;justify-content:space-between;align-items:center;margin-top:12px;color:var(--muted);font-size:13px}

    .empty{padding:28px;text-align:center;color:var(--muted)}

    @media (max-width:520px){
      .new-task input[type="text"]{font-size:14px}
      h1{font-size:18px}
    }
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div>
        <h1>To‑Do — JavaScript</h1>
        <p class="lead">Small, fast, accessible. Saves to your browser (localStorage).</p>
      </div>
      <div style="text-align:right">
        <small class="lead">Tasks stored locally • No backend</small>
      </div>
    </header>

    <section class="card">
      <div class="new-task">
        <input id="taskInput" type="text" placeholder="Add a new task and press Enter" aria-label="New task input" />
        <button id="addBtn" class="btn">Add</button>
      </div>

      <div class="controls">
        <div class="filters" role="tablist" aria-label="Filter tasks">
          <button data-filter="all" class="active">All</button>
          <button data-filter="active">Active</button>
          <button data-filter="completed">Completed</button>
        </div>
        <div style="margin-left:auto;display:flex;gap:8px;align-items:center">
          <button id="clearCompleted" class="btn secondary">Clear completed</button>
          <button id="exportBtn" class="btn secondary">Export JSON</button>
        </div>
      </div>

      <div id="list" class="list"></div>

      <div class="meta">
        <div id="count">0 tasks</div>
        <div style="display:flex;gap:8px;align-items:center">
          <button id="importBtn" class="icon-btn" title="Import JSON">Import</button>
          <input id="fileInput" type="file" accept="application/json" style="display:none" />
        </div>
      </div>

    </section>

  </div>

  <script>
  (function(){
    // Simple, well-documented todo app
    const LS_KEY = 'todo-tasks-v1';
    const taskInput = document.getElementById('taskInput');
    const addBtn = document.getElementById('addBtn');
    const listEl = document.getElementById('list');
    const filters = document.querySelectorAll('[data-filter]');
    const clearCompletedBtn = document.getElementById('clearCompleted');
    const countEl = document.getElementById('count');
    const exportBtn = document.getElementById('exportBtn');
    const importBtn = document.getElementById('importBtn');
    const fileInput = document.getElementById('fileInput');

    let tasks = load();
    let currentFilter = 'all';

    function save(){ localStorage.setItem(LS_KEY, JSON.stringify(tasks)); }
    function load(){ try{ return JSON.parse(localStorage.getItem(LS_KEY) || '[]') }catch(e){return []} }

    function uid(){ return Date.now().toString(36) + Math.floor(Math.random()*1000).toString(36); }

    function addTask(text){
      const trimmed = text.trim();
      if(!trimmed) return;
      tasks.unshift({ id: uid(), text: trimmed, completed:false, createdAt: Date.now() });
      save(); render();
    }

    function updateTask(id, patch){
      tasks = tasks.map(t => t.id===id? {...t,...patch}:t);
      save(); render();
    }
    function removeTask(id){
      tasks = tasks.filter(t=>t.id!==id);
      save(); render();
    }

    function clearCompleted(){
      tasks = tasks.filter(t=>!t.completed); save(); render();
    }

    function setFilter(f){ currentFilter=f; filters.forEach(b=>b.classList.toggle('active', b.dataset.filter===f)); render(); }

    function filteredTasks(){
      if(currentFilter==='active') return tasks.filter(t=>!t.completed);
      if(currentFilter==='completed') return tasks.filter(t=>t.completed);
      return tasks;
    }

    function render(){
      const data = filteredTasks();
      listEl.innerHTML = '';
      if(data.length===0){ listEl.innerHTML = '<div class="empty">No tasks yet — add one above ✨</div>'; }
      data.forEach(t=>{
        const item = document.createElement('div'); item.className='task';
        const left = document.createElement('div'); left.className='left';

        const cb = document.createElement('button'); cb.className='checkbox' + (t.completed? ' checked':''); cb.setAttribute('aria-pressed', String(!!t.completed));
        cb.title = t.completed? 'Mark as active' : 'Mark as completed';
        cb.addEventListener('click', ()=> updateTask(t.id, { completed: !t.completed }));
        cb.innerHTML = t.completed? '&#10003;':'';

        const text = document.createElement('div'); text.className='text' + (t.completed? ' completed':''); text.textContent = t.text;
        text.title = 'Double-click to edit';
        text.addEventListener('dblclick', ()=> startEdit(t, text));

        left.appendChild(cb); left.appendChild(text);

        const actions = document.createElement('div'); actions.className='actions';
        const editBtn = document.createElement('button'); editBtn.className='icon-btn'; editBtn.title='Edit'; editBtn.innerHTML='✎';
        editBtn.addEventListener('click', ()=> startEdit(t, text));
        const delBtn = document.createElement('button'); delBtn.className='icon-btn'; delBtn.title='Delete'; delBtn.innerHTML='🗑';
        delBtn.addEventListener('click', ()=>{ if(confirm('Delete this task?')) removeTask(t.id); });

        actions.appendChild(editBtn); actions.appendChild(delBtn);

        item.appendChild(left); item.appendChild(actions);
        listEl.appendChild(item);
      });

      const remaining = tasks.filter(t=>!t.completed).length;
      countEl.textContent = remaining + (remaining===1? ' task left' : ' tasks left');
    }

    function startEdit(task, textEl){
      const input = document.createElement('input'); input.type='text'; input.value = task.text; input.style.width = '100%'; input.style.padding='8px'; input.style.borderRadius='6px';
      textEl.replaceWith(input); input.focus();
      input.setSelectionRange(input.value.length, input.value.length);

      function finish(saveEdit){
        if(saveEdit){ const val = input.value.trim(); if(val) updateTask(task.id, { text: val }); else removeTask(task.id); }
        else render();
      }

      input.addEventListener('blur', ()=> finish(true));
      input.addEventListener('keydown', (e)=>{
        if(e.key==='Enter'){ finish(true); }
        if(e.key==='Escape'){ finish(false); }
      });
    }

    // keyboard & buttons
    addBtn.addEventListener('click', ()=>{ addTask(taskInput.value); taskInput.value=''; taskInput.focus(); });
    taskInput.addEventListener('keydown', e=>{ if(e.key==='Enter'){ addTask(taskInput.value); taskInput.value=''; }});

    filters.forEach(b=> b.addEventListener('click', ()=> setFilter(b.dataset.filter)));
    clearCompletedBtn.addEventListener('click', ()=>{ if(confirm('Remove all completed tasks?')) clearCompleted(); });

    exportBtn.addEventListener('click', ()=>{
      const data = JSON.stringify(tasks, null, 2);
      const blob = new Blob([data], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = 'todos.json'; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    });

    importBtn.addEventListener('click', ()=> fileInput.click());
    fileInput.addEventListener('change', ()=>{
      const f = fileInput.files[0]; if(!f) return; const reader = new FileReader();
      reader.onload = ()=>{
        try{ const imported = JSON.parse(reader.result);
          if(Array.isArray(imported)){
            // basic validation: assume array of {text}
            const importedTasks = imported.map(t=>({ id: uid(), text: String(t.text||t), completed: !!t.completed, createdAt: t.createdAt || Date.now() }));
            tasks = importedTasks.concat(tasks);
            save(); render();
            fileInput.value = '';
          } else alert('Invalid JSON — expected an array');
        } catch(e){ alert('Failed to parse JSON'); }
      };
      reader.readAsText(f);
    });

    // initial render
    render();

    // expose for debugging (optional)
    window._todo = { addTask, tasks, save, load };
  })();
  </script>
</body>
</html>

```
## OUTPUT
<img width="1910" height="911" alt="image" src="https://github.com/user-attachments/assets/8b16b3a6-e588-46e4-9f6b-d97fdfb67180" />
<img width="1073" height="498" alt="image" src="https://github.com/user-attachments/assets/6c49b63a-7d59-4065-82cb-c0584ea8e03d" />
<img width="1223" height="599" alt="image" src="https://github.com/user-attachments/assets/138c42c2-6b40-42e5-baaa-f6e9ca13df4c" />

## RESULT
The program for creating To-do list using JavaScript is executed successfully.

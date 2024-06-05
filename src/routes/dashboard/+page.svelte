<script>
  import { authHandlers, authStore } from "../../store/store";
  import { getDoc, doc, setDoc} from "firebase/firestore";
 import { db } from "../../lib/firebase/firebase";

    let todoList = [];
    let currTodo = "";
    let error = false;

    authStore.subscribe(curr => {
        todoList = curr.data.todos;
    });

    function addTodo() {
        error = false;
        if(!currTodo) {
            error = true;
        }
        todoList = [...todoList, currTodo];
        currTodo = "";
     }

     function editTodo(index){
        let newTodoList = [...todoList].filter((val, i) => {
            console.log(i, index, i !== index);
            return i != index;
        });
        currTodo = todoList[index]
        todoList = newTodoList
     }

     function deleteTodo(index){
        let newTodoList = [...todoList].filter((val, i) => {
            console.log(i, index, i !== index);
            return i != index;
        });
        todoList = newTodoList
     }

async function saveTodos() {
    try {
        const userRef = doc(db, "users", $authStore.user.uid);
        await setDoc(userRef,
    {
        todos: todoList,
    }, {merge : true});
    } catch (err) {
        console.log("Apologies! Information unable to be saved");
    }
}
</script>

{#if !$authStore.loading}

<div class="mainCont">
    <div class="headCont">
        <h1>Todo List</h1>
        <div class = "hdrbtn">
            <button on:click={saveTodos}><i class="fa-solid fa-floppy-disk"></i>
                <p>Save</p>
            </button>
            <button on:click={authHandlers.logout}>
                <i class="fa-solid fa-right-from-bracket"></i>
                <p>Logout</p>
            </button>

        </div>
    </div>

    <main>
        {#if todoList.length === 0}
        <p>
            Congrats! You have completed all the goals you set.
        </p>
        {/if}
        {#each todoList as todo, index}
        <div class="todo">
            <p>
                {index+1}. {todo}
            </p>
            <div class = "actions">
                <i on:click={() => {editTodo(index);}} on:keydown={() => {}} class="fa-solid fa-pen"></i>
                <i on:click={() => {deleteTodo(index);}} on:keydown={() => {}} class="fa-solid fa-trash"></i>
            </div>
        </div>
        {/each}
    </main>

    <div class={"enterTodo " + (error ? "errorBorder" : "")}>
        <input bind:value={currTodo} type="text" placeholder="Enter item">
        <button on:click={addTodo}>Add</button>
    </div>
</div>
{/if}

<style>
    .mainCont {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        gap: 24px;
        padding: 24px;
        width: 100%;
        margin: 0 auto;
    }

    .headCont{
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: 1.5rem;
    }

    .headCont button {
        background: #04273d;
        color: white;
        padding: 14px 35px;
        border: none;
        border-radius: 25px;
        font-size: 1rem;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 10px;
        cursor: pointer;
    }
    .headCont button i {
        font-size: 1.2rem;
    }
    .headCont button:hover {
        background: rgb(92, 186, 165);
    }

    .hdrbtn {
        display: flex;
        align-items: center;
        gap: 14px;
    }

    main {
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;
    }

    .todo {
        padding: 2px;
        font-size: 1.3rem;
        display: flex;
        align-items: center;
        justify-content: space-between ;
    }

    .enterTodo {
        display: flex;
        align-items: stretch;
        border: 1px solid cyan;
        border-radius: 45px;
        overflow: hidden;
    }
      .errorBorder{
        border-color: red;
    }

   .enterTodo input {
    background: transparent;
    border: none;
    padding: 24px;
    flex:1;
    font-size: 1rem;
    color: white;
   }

   .enterTodo input:focus {
    outline: none;       
   }

   .enterTodo button {
    padding: 0 34px;
    background: #04273d;
    border: none;
    font-size: 1.2rem;
    color: rgb(240, 245, 245);
    font-weight: 200;
    cursor: pointer;
    }

    .enterTodo button:hover {
        background: rgb(92, 186, 165);
    }

  .actions {
    display: flex;
    align-items: center;
    gap: 18px;
    font-size: 1.2rem;
    cursor: pointer;
  }
  
  .actions i:hover {
    color: rgb(169, 5, 5);
  }
   
</style>
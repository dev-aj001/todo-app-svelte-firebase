<script>
    import { initializeApp } from "firebase/app";
    import { getFirestore, collection, query, where, onSnapshot, doc, updateDoc, deleteDoc, addDoc  } from "firebase/firestore";
    import { firebaseConfig } from '$lib/firebase';

    const app = initializeApp(firebaseConfig);

    const db = getFirestore(app);

    const collRef = collection(db, "todos");

    let todos = [];

    const unsubscribe = onSnapshot(collRef, (querySnapshot) => {
        let fbTodos = [];
        querySnapshot.forEach((doc) => {
            let todofb = {...doc.data(), id:doc.id};
            fbTodos = [todofb, ...fbTodos];
            //todos.push(doc.data().name);
        });
        todos = fbTodos;
        //console.table(fbTodos);
    });

    // let promise = fetchData();

    // async function fetchData() {
    //     return new Promise((resolve, reject) => {
    //         const unsubscribe = onSnapshot(collRef, (querySnapshot) => {
    //             let fbTodos = [];
    //             querySnapshot.forEach((doc) => {
    //                 let todofb = {...doc.data(), id: doc.id};
    //                 fbTodos = [todofb, ...fbTodos];
    //             });
    //             resolve(fbTodos);
    //         });
    //      return unsubscribe;
    //     });
    // }

    function handleClick() {
        promise = fetchData();
    }

    //console.log(app, db);

    let task = "";

    const addTodo = async () => {
        if(task){
            await addDoc(collection(db, "todos"), {
                task: task,
                isComplete: false,
                createdAt: new Date(),
            });

            task = "";
        }else{
            alert("Please enter a task to add a new todo");
        }
    }

    const maskAsComplete = async(item) => {
        //todos[index].isComplete = !todos[index].isComplete;
        await updateDoc(doc(db, "todos", item.id), {
            isComplete: !item.isComplete
        });

    }

    const deleteTodo = async (item) => {
        //todos = [...todos.slice(0,index), ...todos.slice(index+1)];
        await deleteDoc(doc(db, "todos", item.id));
    }

    const enterKey = (e) => {
        if (e.key == "Enter") addTodo();
        console.log(e);
    }

</script>

<input type="text" on:keydown={enterKey} placeholder="Add a task" bind:value={task}/>
<button on:click={addTodo}>add</button>

<!-- <ol>
    {#await promise}
        <p>Loading!</p>
    {:then todos} 
        {#each todos as item}
            <li class:complete={item.isComplete}>
                <span>{item.task}</span>
                <span>
                    <button on:click={() => maskAsComplete(item)}>complete</button>
                    <button on:click={() => deleteTodo(item)}>delete</button>
                </span>
            </li>
        {:else}
            <p>No todos found, add a new todo!!</p>
        {/each}       
    {/await}
</ol> -->

<ol>
    {#each todos as item}
        <li class:complete={item.isComplete}>
            <span>{item.task}</span>
            <span>
                <button on:click={() => maskAsComplete(item)}>complete</button>
                <button on:click={() => deleteTodo(item)}>delete</button>
            </span>
        </li>
    {:else}
        <p>No todos found, add a new todo!!</p>
    {/each}  
</ol>


<style>
    .complete {
        text-decoration: line-through;
    }
</style>
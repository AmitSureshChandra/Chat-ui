<template>

    <div>
        <Nav />
        <div class="container">
            <div class="message left">
                Friends .. We all can chat here 
                <span style="color : rgb(58, 73, 204)">
                        (Amit Kesarwani)
                </span>
            </div>
        </div>

        <div class="send">
            <form action="#" id="send-container" @submit.prevent="send"  >
                <input type="text" name="messageInp" id="messageInp" v-model="message">
                <button class="btn" type="submit">Send</button>
            </form>
        </div>
    </div>
</template>

<script>

import Nav from './Nav'
import io from "socket.io-client";
const socket = io('http://localhost:5000');
var audio = new Audio('media/chat.mp3')

export default {

    components : {
        Nav
    },  
    data(){
        return{
            message : '',
        }
    },
    mounted(){

        let name = null;

        // ask new user for name

        name = prompt('Enter name to join')

        while(!name){
            name = prompt('Please enter username to carry forward otherwise close tab')
        }
        
        // broadcast to all new user has joined
        socket.emit('new-user-joined',name)

        // show message new user joined
        socket.on('user-joined', name => {
            this.append(`${name} joined chat`,'left')
        })

        // show msg received from other user
        socket.on('receive', data => {
            this.append(`${data.message} : ${data.name}`,'left')
        })

        // show message some user disconnected
        socket.on('leave', name => {
            this.append(`${name} left chat`,'left')
        })
    },
    methods : {
        append(message,position){
            const messageElement = document.createElement('div')
            messageElement.innerText = message
            messageElement.classList.add('message')
            messageElement.classList.add(position)
            document.querySelector('.container').append(messageElement)
            if(position === 'left'){

                audio.play()
                
                // player.play('media/chat.mp3', function (err) {
                //     if (err) throw err;
                //     console.log("Audio finished");
                //  });
            }
        },
        send(){
            this.append(`You : ${this.message}`,'right')
            socket.emit('send',this.message)
            this.message = ""
        }
    }
}
</script>

<style>

</style>
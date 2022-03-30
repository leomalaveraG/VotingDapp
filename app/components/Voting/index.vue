<template>
  <div> 
     <div v-if="hash!=null" class="w-full h-12 border-8">
      <input class="border-2 rounded px-3 py-2 h-8 w-full focus:outline-none focus:shadow"

      type="text" name="" id="txResult">
    </div>

<div class="flex">
  <div  v-for="index in candidates" :key="index" :class="index.color" class=" text-white p-24 w-1/3 h-screen flex items-center justify-center flex-col">
   <img :src='index.image' class="mb-3">
    <button  v-on:click="vote(index.id)" class="text-3xl uppercase border-2 p-3 hover:bg-green-500 bg-border-blue-500">Vote "{{index.id}}"</button>
    <h1 class="text-3xl mt-3">{{index.votes}}</h1>
  </div>
  
</div>
   
  </div>
</template>
<script>
import { ethers } from "ethers";


import Voting from "../../../build/contracts/Voting.json";
export default {

    //  I choose to have the data here and not load to IFPS
  data() {
    return {
      hash: null,
      candidates: [
      {
        id: 1,
        name:"Laura",
        image:"https://avataaars.io/?avatarStyle=Blank&clotheType=ShirtVNeck&facialHairType=Blank&hairColor=Black&mouthType=Twinkle&skinColor=Light&topType=LongHairBigHair",
        color: "bg-blue-200",
        votes: 0
      },
      {
        id: 2,
        name:"Carlos",
        image:"https://avataaars.io/?avatarStyle=?accessoriesType=Prescription02&avatarStyle=Circle&clotheColor=Blue02&clotheType=BlazerSweater&eyeType=Wink&eyebrowType=FlatNatural&facialHairColor=Black&facialHairType=BeardLight&graphicType=Diamond&hairColor=Blue&hatColor=Blue01&mouthType=Twinkle&skinColor=Light&topType=Hat",
        color: "bg-red-200",
        votes: 0
      },
      {
        id: 3,
        name:"Jaime",
        image:"https://avataaars.io/?accessoriesType=Round&avatarStyle=Circle&clotheColor=Black&clotheType=ShirtVNeck&eyeType=Default&eyebrowType=Default&facialHairColor=Blonde&facialHairType=BeardMajestic&graphicType=Diamond&hairColor=Blue&hatColor=Blue01&mouthType=Default&skinColor=Pale&topType=NoHair",
        color: "bg-teal-200",
        votes: 0
      }
      ],
      CONTRACT_ADDRESS: "0x5B217cc721BD6FF5B568b270E6383cC5E74Ce864",
    };
  },
  mounted() {
        this.refreshList()
  },
  created() {
    // to do
    // Implement Websocket to listen contract events (VOTE EVENT)
  },
  methods: {

    async vote(candiateid) {

        try {
           if (ethereum) {
              console.log("Ethereum Object Found!");
              let provider = new ethers.providers.Web3Provider(ethereum);
        
              await provider.send("eth_requestAccounts", []);
              const signer = provider.getSigner();
              console.log("Account:", await signer.getAddress());
              let connectedContract = new ethers.Contract(
                this.CONTRACT_ADDRESS,
                Voting.abi,
                signer
              );

              let confirmation = window.confirm('Vote for ' + candiateid);
              if (confirmation) {
                // console.log(connectedContract.vote(candiateid).hash);
                let candidate =  await connectedContract.vote(candiateid).then(function(tx){
                    console.log(tx.hash);
                    
                }
                )
              }
           }

        } catch (error) {
          
        }
    },
    async refreshList() {
      console.log("Start");
      try {
        const { ethereum } = window;

        if (ethereum) {
          console.log("Ethereum Object Found!");
          let provider = new ethers.providers.Web3Provider(ethereum);
    

          await provider.send("eth_requestAccounts", []);
          const signer = provider.getSigner();
          console.log("Account:", await signer.getAddress());
          // Create new Contract Instance  > Contract Address, ABI JSON, Signer. 
          let connectedContract = new ethers.Contract(
            this.CONTRACT_ADDRESS,
            Voting.abi,
            signer
          );
          const options = { gasLimit: 150000  }





        console.log(this.candidates.length);
          for (let index = 1; index <= this.candidates.length; index++) {
              
              let votes = await connectedContract.countVotes(index)
              console.log("candiate:" , index , "votes", votes)
              this.candidates[index-1].votes=parseInt(votes)
   
          }




        } else {
          console.log("Ethereum object doesn't exist!");
        }
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>

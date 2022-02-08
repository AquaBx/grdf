<script>
    import Card from "$lib/Card.svelte"
    import Social from "$lib/Social.svelte"
    import Parser from "$lib/Parser.svelte"
    import Container from "$lib/Modal.svelte"


    function fusion(str1,str2){
        let list = str1.split("\n")
        let last = list[list.length-1]

        
        
        if (str1 == undefined && str2 == undefined){
            return ""
        }
        else if (str1 == undefined){
            return str2
        }
        else if (str2 == undefined){
            return str1
        }

        let spl1 = last.split(" ")
        let val1 = spl1[3]
        let spl2 = str2.split(" ")[1]

        if (val1 == spl2){
            spl1[3] = spl2
            last = " ".join(spl1)
            console.log(list)
            return (list)
        }
        else{
            return (str1 + "\n" + str2)
        }
        
    }

    function clear_f (list) {
    	let nlist = []
    	let nslist = {"data":[]}
    	let res = 0

    	let last_0 = ""
    	nlist.push({"title":list[0][0]["5"],"start":list[0][1]["24"],"end":list[0][1]["38"]})

    	for (let i=0;i<list.length;i++){
    		let ni = 2*i+1
        
    		let item = list[ni]
    		let item2 = list[ni+1]

    		if (item2 != undefined && item != undefined){
    			nslist["title"] = item["0"]

    			for (let it of item2){
    				if (it["0"] == undefined){
    					it["0"] = last_0
    				}                    

    				last_0 = it["0"]
    				nslist["data"].push(it)
    			}

    			nlist.push(nslist)
    			nslist = {"data":[]}
    		}	
    	}
    	return nlist
    }

    function clearl(list){
    	let nline = {}
    	let i = 0
    	for (let item of list) {
    		if (item != "" && item != " "){
    			nline[i] =item
    		}
    		i+=1
    	}
    	return nline
    }

    function cleart(list){
    	let ntab = []
    	let nline = []
        let skip = false
        let i = 0
    	for (let item of list) {
    		if(item["0"] != "Planning non validé"){
                if(nline.length>0){
                    let q = nline.length-1
                    if (item["7"] == nline[q]["7"] && item["0"] == undefined && i>2){
                        nline[q]["38"] = fusion(nline[q]["38"],item["38"]) 
                        skip = true
                    }
                }
    			
                if(!skip){
                    if (item["0"] != undefined && Object.keys(item).length == 1){
    			    	ntab.push(nline)
    			    	ntab.push(item)
    			    	nline = []
    			    }
    			    else if (Object.keys(item).length != 0 && item["6"] == undefined){
    			    	nline.push(item)
    			    }
                }
                skip = false
    		}	
            i+=1
    	}
    	return ntab
    }

    function parse(string) {
    	let tab = string.split("\r\n")
    	let ntab = []

    	for (let line of tab) {
    		let nline = line.split(";")
    		ntab.push(clearl(nline))
    	}

    	let res = clear_f(cleart(ntab))

    	let out = document.querySelector("#out")

    	let title = document.createElement("h1")
        title.setAttribute("class","text-3xl text-center mt-2")
    	let subtitle = document.createElement("h2")
        subtitle.setAttribute("class","text-2xl text-center mb-2")

    	title.innerText = res[0]["title"]
    	subtitle.innerText = "du " + res[0]["start"] + " au " + res[0]["end"] 

    	out.appendChild(title)
    	out.appendChild(subtitle)


        let allTable = document.createElement("div")
    	let row0 = []

    	for (let i=1;i<res.length;i++){
    		let item = res[i]
    		let div = document.createElement("div")
    		let h3 = document.createElement("h3")
            h3.setAttribute("class","text-xl text-center m-2 mt-8")
    		let tab = document.createElement("table")
            tab.setAttribute("class","w-[100%] text-[0.5rem]")

    		h3.innerText = item["title"]
    		let data = item["data"]

    		for (let el of data){
    			if(el["0"] == "Fonction"){
    				row0 = ["0","7","38"]
    				//for (let key in el){
    				//	row0.push(key)
    				//}
    			}
    			else{
    				let tr = document.createElement("tr")
    				for (let id of row0){
    					let td = document.createElement("td")
                        td.setAttribute("class","border text-center p-2")
    					td.innerText = el[id]
    					tr.appendChild(td)
    				}
    				tab.appendChild(tr)
    			}
    		}

            div.setAttribute("class","block max-w-[800px] mx-auto")
    		div.appendChild(h3)
    		div.appendChild(tab)
    		allTable.appendChild(div)
    	}
        allTable.setAttribute("class","text-center")
        out.appendChild(allTable)
    }

    export let files

    export let send = () => {
        let file0 = files[0]
        let file1 = files[1]
        const reader = new FileReader();
        reader.addEventListener('load', (event) => {
          parse(event.target.result);
        });
        reader.readAsText(file0);
        document.querySelector("#modal").remove()
    }  

</script>

<svelte:head>
    <title>GRDF</title>
    <meta name="description" content="">
</svelte:head>

<div id="out">
</div>

<div id="modal" class="w-[100%] h-[100%] top-0 left-0 absolute grid place-items-center bg-black opacity-95">    
    <div class="p-5 absolute border">    
        <h1 class="text-4xl">Veuillez Sélectionner le tableau</h1>
        <h3 class="text-1xl">Vérifiez qu'il soit en format CSV et UTF-8</h3>
        <input bind:files={files} class="my-5" type="file" id="file-selector" multiple>
        <button on:click={send} class="block border px-2 py-1 hover:bg-[#EE6F57] transition-colors">Envoyer</button>
    </div>
</div>

<footer class="font-bold text-lg text-center py-8">
    Copyright © 2018-{(new Date()).getFullYear()} <a href="https://www.aquabx.ovh" class="text-[#3282B8] hover:text-[#EE6F57] transition-colors">AquaBx</a>. Tous droits réservés
</footer>

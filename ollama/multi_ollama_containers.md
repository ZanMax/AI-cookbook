# Multiple Ollama Containers on a single host (with multiple GPUs) 

### I don't want model RELOAD
- I have a large machine with 2 GPUs and a considerable amount of RAM. 
- I was trying to use ollama to server `llava` and `mistral` BUT it would reload the models every time I switched model requests. 
- So this is the solution that appears to be working: Multiple Containers, each serving a different model, on different ports. 

#### Ollama model working dir: 
- I have many models already downloaded on my machine so I mount the host ollama working dir to the containers. 
- Linux (At least on my linux machine) - `/usr/share/ollama/.ollama`
- In container: `/root/.ollama`

#### The Docker Run Command:
- `docker run -e CUDA_VISIBLE_DEVICES=0 -d --gpus=all -v /usr/share/ollama/.ollama:/root/.ollama -p
 11434:11434 --name ollama00 ollama/ollama`
- `-e` This is the environmental variable flag to set an ENV VAR. 
- The `CUDA_VISIBLE_DEVICES=0` locks this container done to the first GPU.
- `--gpus=all` still limited by the `CUDA_VISIBLE_DEVICES=0` 
- `-v` is the volume to mount from the HOST/CONTAINER so for me: `/usr/share/ollama/.ollama` is where I already have a ton of models downloaded and I don't want to download them again inside the container.
- `-p` port number this again is HOST/CONTAINER and it is important to change the host port number is have multiple contianers serving ollama
- `--name` give it a good name. in my case `ollama00` just so I know it's the one on GPU 0 
#### And my second container: 
- `docker run -e CUDA_VISIBLE_DEVICES=1 -d --gpus=all -v /usr/share/ollama/.ollama:/root/.ollama -p 11435:11434 --name ollama01 ollama/ollama`
- Change the `CUDA_VISIBLE_DEVICES=1` to 1 for my second GPU
- Give it a different port number (HOST/CONTAINER) 
- Give is a different name, in this case `ollama01` for my second GPU
#### Bonus CPU only Container (It is definitely not as fast, but it works and I am up to three models that can be called with out 'reload': 
- `docker run -d -v /usr/share/ollama/.ollama:/root/.ollama -p 11436:11434 --name ollama03 ollama/o
llama`


 
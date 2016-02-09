Code that should be added to line 29 in TorLoader.java
```
                ProcessBuilder pb=null;
                String os = System.getProperty("os.name").toLowerCase(); // Operating System details as a CASE INSENSTIVE string
            	Logger.log(Logger.NOTICE, "TorLoader ", "Checking OS: "+os);
            	// PROCESSBUILDER settings for WINDOWS
				if (os.indexOf("win") >= 0) {
	            	Logger.log(Logger.NOTICE, "TorLoader", "Start portable tor in windows");
			        // if so, then start tor.exe with torrc.txt
			        pb = new ProcessBuilder(new String[] {
			        		Config.BASE_DIR+"Tor/tor.exe", "-f", "torrc.txt"
			        });
			    // PROCESSBUILDER settings for LINUX and UNIX
				} else if(os.indexOf("nix") >= 0 || os.indexOf("nux") >= 0) {
	            	Logger.log(Logger.NOTICE, "TorLoader", "Start portable tor in *nix or linux");
			        pb = new ProcessBuilder(new String[] {
			        		"tor", "-f", Config.BASE_DIR+"torrc.txt" //, "HiddenServiceDir", Config.BASE_DIR+"hidden_service"
			        		});				
				        } 
				pb.directory(new File(Config.BASE_DIR+"Tor/").getAbsoluteFile());
				System.out.println(new File(Config.BASE_DIR+"Tor/").getAbsolutePath());
```

to replace

```
					ProcessBuilder pb = new ProcessBuilder(new String[] {
							"Tor/tor.exe", "-f", "torrc.txt"
					});
				pb.directory(new File("Tor/").getAbsoluteFile());
				System.out.println(new File("Tor/").getAbsolutePath());
```


---

In TorLoader.java

REMOVE:
```
		if (System.getProperty("os.name").toLowerCase().indexOf("win") == -1) {
			Logger.log(Logger.NOTICE, "TorLoader", "Error: Not windows.");
			return;
		}
		if (!new File("Tor/tor.exe").exists()) {
			Logger.log(Logger.NOTICE, "TorLoader", "Error: Tor/tor.exe not found.");
			return;
		}
```


---


The other modification to the source to enable portable Linux in config.java is

```
			/*
			 * Check if we should Load Portable Tor (cannot if in windows and lacking tor.exe)
			 */
            // Check if missing windows or linux portable files. If so, then disable portable tor loading.
            if (	((System.getProperty("os.name").toLowerCase().indexOf("win") >= 0) && !new File(Config.BASE_DIR+"Tor/tor.exe").exists())		){// Windows check
                Logger.log(Logger.NOTICE, "Config", "Error: Tor/tor.exe (win*) not found. - Recommend configering your own tor hidden services");
                loadTor = false;
            } else {
            	loadTor = Boolean.parseBoolean(assign("loadTor", "true", prop));
            }
            if (loadTor) {
							SOCKS_PORT = assignInt("SOCKS_PORT", 11157, prop);
							LOCAL_PORT = assignInt("LOCAL_PORT", 8975, prop);
			} else { // this will trigger a prompt for user to enter sock numbers
							SOCKS_PORT = assignInt("SOCKS_PORT", -1, prop);
							LOCAL_PORT = assignInt("LOCAL_PORT", -1, prop);
			}	
```

REPLACING

```
			if (System.getProperty("os.name").toLowerCase().indexOf("win") >= 0) {
				SOCKS_PORT = assignInt("SOCKS_PORT", 11157, prop);
				LOCAL_PORT = assignInt("LOCAL_PORT", 8975, prop);
				loadTor = Boolean.parseBoolean(assign("loadTor", "true", prop));
			} else {
				SOCKS_PORT = assignInt("SOCKS_PORT", -1, prop);
				LOCAL_PORT = assignInt("LOCAL_PORT", -1, prop);
				loadTor = false;
			}
```


---



The tor.sh has
```
#!/bin/sh

trap 'kill -15 `cat tor.pid`' 15 #when this script closes, kill tor hidden service

export PATH=$PATH:/usr/sbin # set some path var - not sure what it does
tor -f torrc.txt --PidFile tor.pid & # start linux tor with torrc.txt
wait # wait forever
```
path = require 'path'
{spawn, exec} = require 'child_process'

try
	fs = require 'fs.extra'
catch e
	fs = require 'fs'


bold = `'\033[0;1m'`
green = `'\033[0;32m'`
reset = `'\033[0m'`
red = `'\033[0;31m'`

config = 
	destDir: "build",
	srcDir: "lib"
	staticResources: [		
		# lib files
		{ src: "lib/styles/main.css", dest: "build/styles/main.css" }
		{ src: "lib/index.html", dest: "build/index.html" }
	]

log = (message, color, explanation) ->
	console.log color + message + reset + (explanation or '')

launch = (cmd, options=[], callback) ->
	command = "#{cmd} #{options.join(' ')}"
	app = exec command, (err, stdout, stderr) ->
		log stdout, green
		log stderr, red if err

	app.on "exit", (status) ->
		callback?() if status is 0

build = (watch, callback) ->
	if typeof watch is 'function'
		callback = watch
		watch = false

	options = [ "-c", "-o", "#{config.destDir}/scripts", "#{config.srcDir}/scripts" ]
	options.unshift "-w" if watch

	launch "coffee", options, ->
		# move vendor and other static files over
		fs.copyRecursive "vendor/bootstrap", "build/bootstrap", ->
			fs.copyRecursive "vendor/jasmine", "build/jasmine", ->		
				for resource in config.staticResources
					fs.mkdirpSync path.dirname(resource.dest)
					if fs.existsSync resource.dest
				 		fs.unlinkSync resource.dest

					fs.linkSync resource.src, resource.dest

				# pesky -p folder on Windows				
				fs.rmdir "-p", ->
					callback?()

task "init", "initialize/update environment", ->
	log "Initializing workspace...", bold
	launch "npm", [ "install" ], ->
		log "Environment has been initialized.", green

task "build", "compile project", ->
	build ->
		log "Done", green

task "test", "compile and run jasmine tests", ->
	build ->
		log "Done...opening spec runner...", green
		log __dirname, bold
		require('openurl').open "file:///#{__dirname}/build/jasmine/runner.html"

task "watch", "compile project and watch folder", ->
	build true, ->
		log "Done", green
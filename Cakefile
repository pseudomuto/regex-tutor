fs = require 'fs'
path = require 'path'
{spawn, exec} = require 'child_process'

bold = `'\033[0;1m'`
green = `'\033[0;32m'`
reset = `'\033[0m'`
red = `'\033[0;31m'`

config = 
	destDir: "build",
	srcDir: "lib",
	requiredDirs: [ "build/bootstrap" ]
	staticResources: [
		# bootstrap
		{ src: "vendor/bootstrap/css/bootstrap.min.css", dest: "build/bootstrap/css/bootstrap.min.css" },
		{ src: "vendor/bootstrap/css/bootstrap-responsive.min.css", dest: "build/bootstrap/css/bootstrap-responsive.min.css" },
		{ src: "vendor/bootstrap/img/glyphicons-halflings-white.png", dest: "build/bootstrap/img/glyphicons-halflings-white.png" },
		{ src: "vendor/bootstrap/img/glyphicons-halflings.png", dest: "build/bootstrap/img/glyphicons-halflings.png" },
		{ src: "vendor/bootstrap/js/bootstrap.min.js", dest: "build/bootstrap/js/bootstrap.min.js" },

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
		fs.mkdir config.destDir, ->
			for dir in config.requiredDirs
				fs.mkdirSync dir if not fs.existsSync dir

			for resource in config.staticResources
				# log "#{resource.src} => #{resource.dest}", red
				if fs.existsSync resource.dest
					fs.unlinkSync resource.dest

				fs.mkdirSync path.dirname(resource.dest) if not fs.existsSync path.dirname(resource.dest)
				fs.linkSync resource.src, resource.dest

			callback?()

task "build", "compile project", ->
	build ->
		log "Done", green

task "watch", "compile project and watch folder", ->
	build true, ->
		log "Done", green
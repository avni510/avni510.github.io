task :configure do
  configure()
end

task :upload do
  upload()
end

def configure()
  sh('aws', 'configure')
end

def upload()
  sh(
    'aws', 's3',
    'cp', '_site',
    's3://avnikothari.com/',
    '--recursive'
  )
end

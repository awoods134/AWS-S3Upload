# AWS-S3Upload
This is created to upload a file from the Server attached drive to the S3 Bucket created.


Import-Module AWSPowerShell

$AccessKey = ""
$PrivateKey = ""
$region = "us-east-1"
$bucket = "Venus2018"
$Path = "C:\temp3\"
$Days = "-7"


#Connection Information
Set-AWSCredentials -AccessKey $AccessKey -SecretKey $PrivateKey
#File Processing
$Files = Get-ChildItem -Path "$path" | where-object {$_.LastWriteTime -lt (get-date).AddDays($Days)} | Select-Object FullName,Name,LastWriteTime
ForEach ($File in $Files) 
{
#verify File
$S3bucketfile = get-s3object -BucketName $bucket -key $file.Name
$Filen = $file.Name
#IF Null then Upload file
If (!$S3bucketfile) {
#Upload Object to S3
write-host "Uploading '$Filen'"
Write-S3Object -BucketName $bucket -File $File.FullName 
}
else
{
Write-warning "$Filen Already Exists"
}

#Delete file after Upload

#verify File
$S3bucketfile = get-s3object -BucketName $bucket -key $file.Name
#If Results come back then run command
IF ($S3bucketfile) 
{
IF ($S3bucketfile.key -eq $File.Name)
{
Remove-Item -Path $File.FullName -WhatIf
}
else
{
Write-Warning "$filen Was not Uploaded" 
}
}

}

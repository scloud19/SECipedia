! MS Configs

Default Config Problems
  DoS Situations
    ! SecUploadFileLimit 32
      Directive Use
        If MS is used to inspect uploaded files (i.e. it's coupled with an anti virus, etc.)
          In this situation, the files will be saved to disk
      Default Value: 100
        This is usually too much

      Exploit
        Easy for an attacker to include many embedded files in a single "multipart/form-data" request
          This would start creating many files on the FS, and could create a DoS situation

      
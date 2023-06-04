65534 İç içe geçim zip dosyasını script yazarak ayırdım

sc= Çıkarttığı dosyayı Tempe dönüştürüp diğer dosyayı siliyor ve bunu sürekli tekrarlayarak 1. zipe ulaştırıyor


SCRİPT : 

def unzipper():
    # Returns directory content.
    directory_content_list = lstr_dir_content()

    # Classifies the elements.
    # classified_elements_list is list of lists
    classified_elements_list = \
        clssfr_file_or_dir(directory_content_list)
    
    # ...after classification, prints sorted and classified directory elements.
    prnt_classified_file_or_dir(classified_elements_list)
    
    prnt_newline()

    unzip_cand_filepath_list = list()
    # glob searches all .zip file inside current working directory
    # then adds paths of them to list.
    unzip_cand_filepath_list = glob.glob(os.path.join(os.getcwd(), "*.zip"))

    if len(unzip_cand_filepath_list) == 0: # If there is no .zip file in dir...
        print("There is no .zip [f]ile(s) in directory!")
        return # ...exits function.
    elif len(unzip_cand_filepath_list) >= 2: # if there is 1+ .zip files.
        print("Directory exists more then one .zip [f]ile(s)!")
        print("Try one by one! Extraction will terminate!")
        return # ...exits function.
    else:
        unzip_cand_file_basename_list = list()

        # Converts added paths to base names, then adds the base name list.
        for file_path in unzip_cand_filepath_list:
            unzip_cand_file_basename_list.append(os.path.basename(file_path))

        unzip_cand_file_basename_list.sort()

        unzip_cand_filename_list_for_print = list()
        # prnt_classified_file_or_dir works with list_of_lists.
        # unzip_cand_filename_list_for_print[0] = unzip_cand_filepath_list
        unzip_cand_filename_list_for_print.\
            append(unzip_cand_file_basename_list)
        # unzip_cand_filename_list_for_print[1] = [] # Blank list.
        unzip_cand_filename_list_for_print.append([])

        prnt_classified_file_or_dir(unzip_cand_filename_list_for_print)

        print("\n... will be extracted!")

        # Returns one of these : "1" or passwd_list_filename
        # Password protected .zip files rises RuntimeError when extracting.
        # If returns 1 (means there is no wordlist necessary), so
        # wordlist attacker do not runs.
        # If returns a wordlist name, when the error rise, wordlist attack
        # will involve the pass cracking.
        one_or_wordlist = pwlist_pwgen_neither()

        remove_yes_no = qry_delete_originals()
        # Will be used on out of y - n deletion option.

        # Time to remove files or directories.
        if remove_yes_no == "y" or remove_yes_no == "yes":
            pass
        elif remove_yes_no == "n" or remove_yes_no == "no":
            backup_zip_files(unzip_cand_filepath_list)
        else: # This block becomes default option for delete
            prnt_default_value_assign("do_not_delete_originals")
            backup_zip_files(unzip_cand_filepath_list)

        # Recursive Unzipper
        while True:
            try:
                unzip_cand_filepath_list = list()
                # glob searches all .zip file inside current working directory
                # then adds to list.
                unzip_cand_filepath_list = \
                    glob.glob(os.path.join(os.getcwd(), "*.zip"))

                # If there is no .zip file in dir,
                if len(unzip_cand_filepath_list) == 0:
                    break
                else:
                    # Every unzip candidate renamed to this below.
                    temp_zip_filename = "temp_zip_filename.zip"

                    # Gets the basename of file from path.
                    founded_zip_filename = \
                        os.path.basename(unzip_cand_filepath_list[0])
                    # Renames the file.
                    os.rename(founded_zip_filename, temp_zip_filename)
                    unzip_cand_f_name = temp_zip_filename

                    # Time to unzip.
                    with zipfile.ZipFile(unzip_cand_f_name, "r") as \
                        new_unzip_candidate:
                        
                        # Extracts working directory.
                        new_unzip_candidate.extractall()
                    
                    # If extraction success, removes.
                    os.remove(temp_zip_filename)
            
            # If unzip candidate protected with password, raises Runtime Error,
            # then runs codes below.
            except RuntimeError:
                # If value int, occurs below.
                # ValueError: invalid literal for int() with base 10: 
                # 'rockyou.txt'
                
                # So value must be str
                
                # If unnecessary option selected in
                # pwlist_pwgen_neither and unzip candidate
                # is actually a password protected .zip:
                if one_or_wordlist == "1":
                    prnt_newline()
                    print(".zip is actually a password protected one!")
                    print("Please, select '2' in next extraction try!")
                    return # Exits in function..
                else: # Wordlist attacker runs.
                    unzip_cand_filepath_list = list()
                    # glob searches all .zip file inside current working 
                    # directory, then adds to list
                    unzip_cand_filepath_list = \
                        glob.glob(os.path.join(os.getcwd(), "*.zip"))

                    # If there is no .zip file in dir,
                    if len(unzip_cand_filepath_list) == 0:
                        break
                    else:

                        # Will be used in extraction.
                        password = None

                        # Every unzip candidate renamed to this below.
                        temp_zip_filename = "temp_zip_filename.zip"

                        # Gets the basename of file from path.
                        founded_zip_filename = \
                            os.path.basename(unzip_cand_filepath_list[0])
                        # Renames the file.
                        os.rename(founded_zip_filename, temp_zip_filename)
                        unzip_cand_f_name = temp_zip_filename

                        # If password not found in wordlist,
                        # terminates the trying. Else, program never stops.
                        list_end_counter = 0

                        # Time to unzip.
                        with zipfile.ZipFile(unzip_cand_f_name, "r") as \
                            new_unzip_candidate:
                            with open(one_or_wordlist, "r") as wlf:
                                wordlist_list = wlf.readlines()
                                # for loop below, must be break
                                # when password found! There is no need to
                                # try other passwords in list.
                                for line in wordlist_list:
                                    password = line.strip("\n")
                                    try:
                                        # (pwd=bytes(password, 'utf-8')) is
                                        # very important. It got my 6 hours.
                                        # Without this, throws encoding error!
                                        # Also password variable data must be
                                        # byte string! Important!

                                        # Extracts working directory.
                                        new_unzip_candidate.\
                                            extractall\
                                                (pwd=bytes(password, 'utf-8'))
                                        
                                        prnt_newline()
                                        print("[+] Password Found: {}".\
                                            format(password))
                                        
                                        os.remove(unzip_cand_f_name)

                                        # break below is important!
                                        # When password founded in wordlist
                                        # breakes the for loop.
                                        # So, other words in wordlist
                                        # will not tried!
                                        break
                                    except:
                                        # Counts one by one for every wrong
                                        # password in given wordlist.
                                        # If passwords ends, terminates the
                                        # trying. Without return, trying never
                                        # ends.
                                        list_end_counter += 1
                                        if list_end_counter == \
                                            len(wordlist_list):
                                            
                                            prnt_newline()
                                            print("[-] Password not found \
in wordlist!")
                                            return # Exits in function.
                                        else:
                                            pass
        
        # Extraction trying done. Lets check the changes.
        # Which [f]ile(s) or [d]ir(s) is/are new?
        dir_content_list_after_extraction = lstr_dir_content()

        new_files_or_dirs = list()
        
        # Finds new [f]ile(s) or [d]ir(s) and adds the list.
        for element in dir_content_list_after_extraction:
            if element not in directory_content_list:
                new_files_or_dirs.append(element)
        
        new_files_or_dirs.sort()

        prnt_newline()
        print("new [f]ile(s) or [d]ir(s) in ...")

        # Classifies the new ones...
        # classified_new_elements_list is list of lists
        classified_new_elements_list = \
            clssfr_file_or_dir(new_files_or_dirs)
        
        # After classification, prints...
        prnt_classified_file_or_dir(classified_new_elements_list)





içerisinde 1. txt olduğunu buluyorum ve şfirelenmiş bir halde 

John zip aracını kullanarak hash değerini alıyorum 

john aracını kullanarak sqlmap.txt bruteforce attack yapıyorum ve şifre önüme düşüyor.


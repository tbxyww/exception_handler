Exception handler
=================

 Exception handler module base on Google breakpad


## Usage


    bool 
    ShowDumpResults (
        const wchar_t* dump_path,
        const wchar_t* minidump_id,
        void* context,
        EXCEPTION_POINTERS* exinfo,
        MDRawAssertionInfo* assertion,
        bool succeeded
        )
    {
        Q_UNUSED(dump_path);
        Q_UNUSED(minidump_id);
        Q_UNUSED(context);
        Q_UNUSED(exinfo);
        Q_UNUSED(assertion);
        Q_UNUSED(succeeded);
        
        QFileInfo CurrentFileInfomation(qApp->applicationFilePath());
        QDir      CurrentDirectory(CurrentFileInfomation.absoluteDir());
        QString   BugReportExe = CurrentDirectory.absoluteFilePath("BugReport.exe");

        QDesktopServices::openUrl(QUrl::fromLocalFile(BugReportExe));

        return true;
    }

    bool initialize_crash_handler() 
    {
        std::wstring current_location;

        current_location.resize(1024);
        GetModuleFileName(NULL, &current_location[0], current_location.size());
        PathRemoveFileSpec(&current_location[0]);

        current_location.resize(wcslen(current_location.c_str()));

        exception_handle_ptr_.reset(
            new google_breakpad::ExceptionHandler(
            current_location,
            NULL,
            ShowDumpResults,
            NULL,
            google_breakpad::ExceptionHandler::HANDLER_ALL,
            MiniDumpWithFullMemory,
            NULL,
            NULL));

        if(exception_handle_ptr_ == NULL) {
            return false;
        }
        return true;
    }

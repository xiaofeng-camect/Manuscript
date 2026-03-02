# Description
Operation: The logs contain user actions and important events and allow users to view.
![Operation_entry1](https://github.com/user-attachments/assets/33e72302-c91d-4f1d-af5f-eb405ff89a5f)
![Operation_entry2](https://github.com/user-attachments/assets/b3b5940c-6c8d-49c6-9496-a838686d430a)

It should contain all of follows:
  1. The setting changes from user action in UI and API, e.g. added a camera.
  2. The server status changes from auto or triggered, e.g. switched operation mode:.

# Add a track object
Call functions in go/src/server/home_server/log_operation.go
## Simple event-logOperation():
![2026-03-02 15-18-22](https://github.com/user-attachments/assets/5d2a3eb4-4826-46e7-88f4-9b5e83e56788)
## Switch button-maybeLogBoolConfigChange():
![2026-03-02 15-25-49](https://github.com/user-attachments/assets/ced8542c-8e25-4a2a-a4a6-4ff227965e0f)
Note:
  1. "isEnable==true" means "val==true" is "enable"
  2. Some button corresponding to a int(float) instead of bool value, e.g. sensitive mode:
     "AiCfg.GetScoreCutoff() == 0" means sensitive mode disabled.
  3. Some corresponding buttons has sub-settings, we need to divide them into two types:
     1) Sub-settings would remain even button switches to disable(enable), e.g. "Showing subjects in email" under "Send alert to email":
        ![2026-03-02 17-52-49](https://github.com/user-attachments/assets/9bb66ca4-fa68-4619-8320-b7414f9cc674)
        ```
      	p.maybeLogBoolConfigChange(true, oldSendEmail, newSendEmail,
        	storage.OperationChangeUserAlertConfigs, "", camId, nil, "Send alert to email: ")
        p.maybeLogStringsConfigChange(oldEmailCfg, newEmailCfg,
        	storage.OperationChangeUserAlertConfigs, "", camId, nil, "Showing subjects in email: ")
        ```
     3) Sub-settings would recover to default when button switches to disable(enable), e.g. :

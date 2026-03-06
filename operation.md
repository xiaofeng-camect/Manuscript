# Description
Operation: The logs contain user actions and important events and allow users to view.
![Operation_entry1](https://github.com/user-attachments/assets/33e72302-c91d-4f1d-af5f-eb405ff89a5f)
![Operation_entry2](https://github.com/user-attachments/assets/b3b5940c-6c8d-49c6-9496-a838686d430a)

It should contain all of follows:
  1. The setting changes from user action in UI and API, e.g. added a camera.
  2. The server status changes from auto or triggered, e.g. switched operation mode:.

# Add a track object
Call functions in go/src/server/home_server/log_operation.go
## Simple event-logOperation(), e.g. "Restarted hub":
## Switch button-maybeLogBoolConfigChange(), e.g. "Long distance mode":
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
     3) Sub-settings would recover to default when button switches to disable(enable), e.g. "Tampering detection schedules" under "Tampering detection":
        ![2026-03-02 18-07-03](https://github.com/user-attachments/assets/dde0c350-79aa-4cb1-96b9-197591b7f76d)
        ```
        if !p.maybeLogBoolConfigChange(true, cfg.TamperingCfg.GetEnableDetection(), req.TamperingCfg.GetEnableDetection(),
          storage.OperationChangeAlertConfigs, "", camId, nil, "Tampering detection: ") {
          maybeLogPbListConfigChange(cfg.TamperingCfg.GetValidity(), req.TamperingCfg.GetValidity(),
            storage.OperationChangeAlertConfigs, p, "", camId, nil, "Tampering detection schedules: ")
        }
        ```
## Number settings-maybeLogInt32ConfigChange(), e.g. "Start seconds before triggering".


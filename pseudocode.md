START RegistrationProcess(Student, SelectedCoursesList)
    // Initialize variables
    TotalCredits = 0
    ApprovedCourses = Empty List
    ErrorMessages = Empty List

    // Start the validation loop for each course
    LOOP: FOR EACH 'Course' IN SelectedCoursesList:

        // 1. Quota Check
        IF Course.CurrentCapacity < Course.MaximumCapacity THEN:

            // 2. Prerequisite Check
            IF Student.HasPassedCourses(Course.Prerequisites) == TRUE THEN:

                // 3. Schedule Conflict Check
                IF HasScheduleConflict(ApprovedCourses, Course.TimeSlot) == FALSE THEN:

                    // 4. Credit Limit Check
                    IF TotalCredits + Course.CreditValue <= Student.MaximumCreditLimit THEN:

                        // Passed all checks, add course to list and update credits
                        ApprovedCourses.Add(Course)
                        TotalCredits = TotalCredits + Course.CreditValue

                    ELSE:
                        ErrorMessages.Add(Course.Name + ": Could not be added due to credit limit.")
                    END IF

                ELSE:
                    ErrorMessages.Add(Course.Name + ": Conflicts with another course in your schedule.")
                END IF

            ELSE:
                ErrorMessages.Add(Course.Name + ": You have not completed the required prerequisites.")
            END IF

        ELSE:
            ErrorMessages.Add(Course.Name + ": Quota is full.")
            // Waitlist function can be called here
        END IF

    END LOOP

    // 5. Advisor Approval Stage
    IF ApprovedCourses.ItemCount > 0 THEN:
        // Send courses to advisor's screen
        AdvisorDecision = SendToAdvisorForApproval(Student, ApprovedCourses)

        IF AdvisorDecision == "APPROVED" THEN:
            SaveToDatabase(Student, ApprovedCourses)
            PRINT "Registration successfully completed."
        ELSE:
            PRINT "Registration pending. Your advisor rejected the draft or requested changes."
        END IF

    ELSE:
        PRINT "Registration failed. None of the selected courses passed the validation steps."
    END IF

    // Show errors to the student
    IF ErrorMessages.ItemCount > 0 THEN:
        PRINT "The following errors occurred:"
        PRINT ErrorMessages
    END IF

END RegistrationProcess

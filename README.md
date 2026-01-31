/* Prevent error color on hover/focus BEFORE user interaction */
.mdc-text-field--invalid.ng-untouched.ng-pristine:hover
.mdc-floating-label,
.mdc-text-field--invalid.ng-untouched.ng-pristine.mdc-text-field--focused
.mdc-floating-label {
  color: var(--mdc-theme-on-surface) !important;
}

/* Also protect input text on hover */
.mdc-text-field--invalid.ng-untouched.ng-pristine:hover
.mdc-text-field__input,
.mdc-text-field--invalid.ng-untouched.ng-pristine.mdc-text-field--focused
.mdc-text-field__input {
  color: var(--mdc-theme-on-surface) !important;
}
